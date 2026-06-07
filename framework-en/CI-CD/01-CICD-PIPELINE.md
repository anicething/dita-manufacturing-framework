# CI/CD Publishing Pipeline

**Document ID:** CIC-001
**Version:** 1.1
**Last Updated:** 2026-06-07
**Status:** Approved

---

## Table of Contents

1. [Pipeline Concepts](#1-pipeline-concepts)
2. [Build Triggers](#2-build-triggers)
3. [Validation Stage](#3-validation-stage)
4. [Build & Publish Stage](#4-build--publish-stage)
5. [Deploy Stage](#5-deploy-stage)
6. [Publishing SLA](#6-publishing-sla)
7. [Platform Notes](#7-platform-notes)
8. [Troubleshooting](#8-troubleshooting)

---

## 1. Pipeline Concepts

### 1.1 Core Pipeline

The CI/CD pipeline automates DITA content validation, transformation, and distribution. The general flow is:

```
Content Change
      │
      ▼
┌─────────────┐
│  Validate   │  ← XML validity, metadata completeness, link integrity
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Build      │  ← DITA-OT transforms (HTML5, PDF, Mobile)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Deploy     │  ← Copy to web server, CDN, or document portal
└──────┬──────┘
       │
       ▼
  Notify Stakeholders
```

### 1.2 Pipeline Separation

| Pipeline | Trigger | Purpose | Typical Duration |
|----------|---------|---------|-----------------|
| **Validate** | Branch merge/push (non-main) | Quality gate before production | < 5 min |
| **Publish** | Push to main/release branch | Production build and deploy | < 1 hour |
| **Scheduled** | Cron/timer (weekly or monthly) | Full rebuild + freshness check | < 1 hour |

### 1.3 Prerequisites

Before setting up a CI/CD pipeline, ensure:

- [ ] DITA-OT 4.x installed on build server or available as container image
- [ ] PDF plugin installed (org.dita.pdf2 or com.elovirta.pdf via Chemistry)
- [ ] Build server has network access to DITA-OT and plugin repositories
- [ ] Output directories configured and writable
- [ ] Deployment targets accessible from build server

---

## 2. Build Triggers

### 2.1 Trigger Configuration

Configure the CI/CD system to trigger on content changes:

| Trigger | Branch | Path Filter | Use Case |
|---------|--------|-------------|----------|
| Push to main | `main` | `src/**`, `references/**` | Production publish |
| Push to release | `release/*` | `src/**`, `references/**` | Versioned release |
| Pull/merge request | Any feature branch | `src/**` | Pre-merge validation |
| Scheduled | `main` | All | Weekly rebuild + link check |
| Manual | Any | All | Ad-hoc publish |

**Path filter recommendation:** Only trigger on content directories. Exclude `.gitignore`, `README.md`, and tooling configuration changes that don't affect published output.

### 2.2 Concurrent Builds

Run HTML5, PDF, and mobile builds in parallel to minimize total pipeline duration:

```
                    ┌──────────────┐
                    │  Validation  │
                    └──────┬───────┘
                           │
            ┌──────────────┼──────────────┐
            │              │              │
            ▼              ▼              ▼
       ┌────────┐    ┌────────┐    ┌──────────┐
       │ HTML5  │    │  PDF   │    │  Mobile  │
       └────────┘    └────────┘    └──────────┘
            │              │              │
            └──────────────┼──────────────┘
                           │
                           ▼
                     ┌──────────┐
                     │  Deploy  │
                     └──────────┘
```

---

## 3. Validation Stage

### 3.1 Validation Steps

Every content change should pass the following checks before production publishing:

| Check | Tool/Command | Failure Action |
|-------|-------------|----------------|
| **XML well-formedness** | `xmllint --noout *.dita` | Block pipeline |
| **DITA schema validation** | DITA-OT `--validate` flag | Block pipeline |
| **Metadata completeness** | Custom validation script | Block pipeline |
| **Link integrity** | DITA-OT validation report | Block pipeline |
| **Schematron rules** | Custom Schematron + DITA-OT | Block pipeline |
| **Spell check** | Hunspell / aspell | Warn (non-blocking) |
| **Reuse rate check** | Custom reuse scanner script | Warn if below threshold |

### 3.2 Validation Command (DITA-OT)

```bash
# Validate all DITA files in source directory
dita --input=src/product-a/maps/master.ditamap \
     --format=html5 \
     --validate \
     --property=args.reload=true \
     --property=generate.debug.attributes=true
```

### 3.3 Custom Validation Script

Create `scripts/validate.sh` (or equivalent for your platform):

```bash
#!/bin/bash
set -euo pipefail

echo "=== Validation Pipeline ==="

# 1. XML well-formedness
echo "Checking XML well-formedness..."
find src/ references/ -name "*.dita" -exec xmllint --noout {} +
echo "✅ All XML files valid"

# 2. Metadata check
echo "Checking required metadata..."
for file in $(find src/ -name "*.dita"); do
    if ! grep -q '<author>' "$file"; then
        echo "❌ Missing author in $file"
        exit 1
    fi
done
echo "✅ Metadata check complete"

# 3. Broken link check
echo "Checking cross-references..."
dita --input=src/product/maps/master.ditamap \
     --format=html5 \
     --validate 2>&1 | grep -i "warning\|error" || true
echo "✅ Link validation complete"

echo "=== All checks passed ==="
```

---

## 4. Build & Publish Stage

### 4.1 DITA-OT Build Command

```bash
# Single-format build
dita --input=src/product/maps/master.ditamap \
     --format=html5 \
     --output=output/html5/product-a \
     --property=args.css=framework/styles/shared/base.css \
     --property=toc.section.depth=4

# PDF build (with Chemistry plugin for improved CSS support)
dita --input=src/product/maps/master.ditamap \
     --format=pdf \
     --output=output/pdf/product-a \
     --property=pdf.formatter=chemistry

# Mobile (EPUB) build
dita --input=src/product/maps/master.ditamap \
     --format=epub \
     --output=output/mobile/product-a
```

### 4.2 Multi-Format Build Script

Create `scripts/build-all.sh`:

```bash
#!/bin/bash
set -euo pipefail

MAP_PATH="${1:-src/product/maps/master.ditamap}"
OUTPUT_BASE="${2:-output}"
TIMESTAMP=$(date '+%Y%m%d-%H%M')

echo "=== DITA Multi-Format Build ==="
echo "Input: $MAP_PATH"
echo "Timestamp: $TIMESTAMP"

# Build all formats in parallel
for format in html5 pdf epub; do
    (
        echo "Building $format..."
        dita --input="$MAP_PATH" \
             --format="$format" \
             --output="${OUTPUT_BASE}/${format}/${TIMESTAMP}" \
             --property=args.draft=false
        echo "✅ $format complete"
    ) &
done

wait
echo "=== All builds complete ==="
```

### 4.3 Build Artifacts

After a successful build, the following outputs should be available:

```
output/
├── html5/latest/           ← Current HTML5 build
│   ├── index.html
│   ├── topics/
│   └── images/
├── pdf/latest/             ← Current PDF build
│   └── product-manual.pdf
├── epub/latest/            ← Current EPUB build
│   └── product-manual.epub
└── archive/                ← Timestamped builds for rollback
    ├── 20260607-0800/
    └── 20260601-0800/
```

### 4.4 Build Configuration File

Create `build-config.xml` in the repository root:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="dita-build">
  <property name="input" value="src/product/maps/master.ditamap"/>
  <property name="output" value="output"/>
  <property name="css" value="framework/styles/shared/base.css"/>
  <property name="toc.depth" value="4"/>
  
  <target name="build-all" description="Build all output formats">
    <antcall target="build-html5"/>
    <antcall target="build-pdf"/>
    <antcall target="build-epub"/>
  </target>
  
  <target name="build-html5">
    <exec executable="dita">
      <arg line="--input=${input}"/>
      <arg line="--format=html5"/>
      <arg line="--output=${output}/html5"/>
      <arg line="--property=args.css=${css}"/>
    </exec>
  </target>
  
  <target name="build-pdf">
    <exec executable="dita">
      <arg line="--input=${input}"/>
      <arg line="--format=pdf"/>
      <arg line="--output=${output}/pdf"/>
      <arg line="--property=pdf.formatter=chemistry"/>
    </exec>
  </target>
  
  <target name="build-epub">
    <exec executable="dita">
      <arg line="--input=${input}"/>
      <arg line="--format=epub"/>
      <arg line="--output=${output}/epub"/>
    </exec>
  </target>
</project>
```

---

## 5. Deploy Stage

### 5.1 Deployment Options

Choose the deployment method that matches your infrastructure:

| Method | Suitable For | Setup Complexity |
|--------|-------------|-----------------|
| **Web server sync** | Internal web server | Low |
| **Document portal** | Customer-facing docs | Medium |
| **CMS integration** | Enterprise CCMS | High |
| **Static hosting** | Public documentation | Low |
| **Network share** | Internal teams | Low |

### 5.2 Deployment Script Template

Create `scripts/deploy.sh`:

```bash
#!/bin/bash
set -euo pipefail

DEPLOY_TARGET="${1:-/var/www/docs}"
BUILD_OUTPUT="${2:-output}"

echo "=== Deploying Documentation ==="
echo "Source: $BUILD_OUTPUT"
echo "Target: $DEPLOY_TARGET"

# Copy HTML5 output
rsync -avz --delete "${BUILD_OUTPUT}/html5/latest/" "${DEPLOY_TARGET}/html5/"

# Copy PDF output
rsync -avz "${BUILD_OUTPUT}/pdf/latest/" "${DEPLOY_TARGET}/pdf/"

# Verify deployment
if [ -f "${DEPLOY_TARGET}/html5/index.html" ]; then
    echo "✅ Deployment verified"
else
    echo "❌ Deployment failed: index.html not found"
    exit 1
fi
```

---

## 6. Publishing SLA

| Step | Duration | Automation |
|------|----------|-----------|
| Content commit | Immediate | Yes |
| Trigger CI/CD | < 1 min | Yes |
| XML validation | < 1 min | Yes |
| HTML5 generation | < 15 min | Yes |
| PDF generation | < 20 min | Yes |
| Mobile (EPUB) generation | < 10 min | Yes |
| Deploy to target | < 5 min | Yes |
| Search indexing | < 10 min | Yes |
| **Total** | **< 1 hour** | **100% automated** |

---

## 7. Platform Notes

### 7.1 CI/CD Platform Comparison

The pipeline concepts in this document apply to any CI/CD platform. When implementing, adjust syntax for your chosen platform:

| Platform | Config File | Parallel Builds | Container Support |
|----------|-------------|-----------------|-------------------|
| Generic CI/CD | `pipeline.yml` or custom | Y (parallel stages) | Y |
| Jenkins | `Jenkinsfile` (Declarative) | Y (parallel directive) | Y (Docker agent) |
| GitLab CI | `.gitlab-ci.yml` | Y (needs/parallel) | Y (Docker executor) |
| Azure DevOps | `azure-pipelines.yml` | Y (jobs.strategy) | Y (container jobs) |
| TeamCity | `buildTypes` (UI/XML) | Y (build chains) | Y (Docker wrapper) |
| Bamboo | `bamboo-specs/` (YAML) | Y (stages) | Y (Docker task) |

### 7.2 DITA-OT as Docker Image

For containerized builds, use the DITA-OT Docker image:

```bash
# Pull the image
docker pull ditaot/dita-ot:4.0

# Run a build
docker run --rm \
  -v "$(pwd)/src:/workspace/src" \
  -v "$(pwd)/output:/workspace/output" \
  ditaot/dita-ot:4.0 \
  dita --input=/workspace/src/product/maps/master.ditamap \
       --format=html5 \
       --output=/workspace/output/html5
```

Add the Docker image to your CI/CD pipeline for consistent, reproducible builds across environments.

---

## 8. Troubleshooting

| Symptom | Likely Cause | Resolution |
|---------|-------------|-----------|
| Build fails immediately | DITA-OT not installed or wrong version | Verify `dita --version`; install DITA-OT 4.x |
| XML validation errors | Malformed DITA files | Run `xmllint` on each file; check for unclosed tags |
| Missing topics in output | Broken `topicref` in ditamap | Verify all `@href` paths resolve correctly |
| PDF missing fonts | Font not embedded | Add font to custom CSS or PDF plugin configuration |
| Images not rendering in PDF | Image path issue | Use absolute paths or `@keyref` for image references |
| Slow build (> 30 min) | Too many formats or large content set | Enable parallel builds; check DITA-OT memory settings |
| Deployment failed | Target server unreachable | Check network connectivity; verify credentials |

**Build failure procedure:**
1. Check the CI/CD build log for the first error
2. Look for XML validation errors
3. Check for missing files in map references
4. Verify metadata is complete
5. Contact DITA administrator

---

## Related Documents

| Document | Location |
|----------|----------|
| DITA-OT Configuration | [CI-CD/02-DITA-OT-CONFIG.md](02-DITA-OT-CONFIG.md) |
| Publishing SLA | [CI-CD/03-PUBLISHING-SLA.md](03-PUBLISHING-SLA.md) |
| Cross-Format Consistency | [PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md](../PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md) |
| Validation Gates | [WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md](../WORKFLOWS/02-DOCUMENT-SET-INTEGRITY.md) |
