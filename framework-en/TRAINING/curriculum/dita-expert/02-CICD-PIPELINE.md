---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# CI/CD Pipeline for DITA
## Automated Publishing and Validation (Platform-Agnostic)

**DITA Expert Level - Module 2**
Example Manufacturing Co., Ltd.
v1.1 | 2026-06-07

---

<!-- _class: divider -->

# Agenda
1. CI/CD Architecture Overview
2. Publish Workflow (publish.yml)
3. Validate Workflow (validate.yml)
4. Custom Validation Scripts
5. DITA-OT Customization
6. Deployment Targets
7. Monitoring and Notifications
8. Troubleshooting CI/CD Failures

---

# CI/CD Architecture: Bird's Eye View

```
Version Control Repository (main branch)
           │
           ├── push to main → TRIGGER: publish.sh
           │       │
           │       ├── Step 1: Checkout code
           │       ├── Step 2: Setup DITA-OT 4.x
           │       ├── Step 3: HTML5 build (per product)
           │       ├── Step 4: PDF build (per product)
           │       ├── Step 5: Upload to deployment target
           │       ├── Step 6: Search indexing
           │       └── Step 7: Team notification
           │
           └── pull request → TRIGGER: validate.sh
                   │
                   ├── XML validation
                   ├── Metadata check
                   ├── Spell check
                   ├── Link integrity check
                   └── Subject Scheme validation
```

---

# CI/CD Components

| Component | Script | Trigger | Runtime |
|-----------|--------|---------|---------|
| **Publish** | `ci-cd/publish.sh` | Push to `main` (paths: `src/**`) | ~50 min |
| **Validate** | `ci-cd/validate.sh` | Pull request (paths: `src/**`) | ~5 min |
| **Schedule** | `ci-cd/scheduled.sh` | Cron (weekly, Sunday 2AM) | ~30 min |

All scripts run on standard CI/CD runners (Linux agents).

---

# Publish Workflow: Trigger Configuration

```bash
#!/bin/bash
# publish.sh - Triggered on push to main when source files change
# Add to CI/CD pipeline configuration (platform-specific trigger)

# This script runs when:
#   - Source files under src/ are modified
#   - Pipeline scripts under ci-cd/ are updated
#   - Framework specialization files (DTD, mod, ent) are changed
#   - catalog.xml is updated

echo "Publish pipeline triggered for branch: $(git rev-parse --abbrev-ref HEAD)"
```

**Key design decisions:**
- Only triggers on content changes (`src/**`), not README updates
- Also triggers on pipeline script changes (so updates get tested)
- Triggers on framework/specialization changes (DTD, mod, ent files)

---

# Publish Workflow: Checkout and Setup

```bash
#!/bin/bash
# Stage 1: Checkout and setup DITA-OT

echo "=== Stage 1: Environment Setup ==="

# Checkout (handled by CI/CD agent)
echo "Repository checked out at: $(pwd)"

# Setup DITA-OT 4.x
echo "Setting up DITA-OT 4.0..."
if [ ! -d "dita-ot-4.0" ]; then
    wget -q https://github.com/dita-ot/dita-ot/releases/download/4.0/dita-ot-4.0.zip
    unzip -q dita-ot-4.0.zip
fi

cd dita-ot-4.0
./bin/dita --version
cd ..
```

---

# Publish Workflow: Multi-Product HTML5

```bash
#!/bin/bash
# Stage 2: HTML5 generation across all products

PRODUCTS=("dr-5000" "dr-8000" "dr-2000")
DITA_OT="./dita-ot-4.0/bin/dita"

echo "=== Stage 2: HTML5 Build ==="

for product in "${PRODUCTS[@]}"; do
    echo "Building HTML5 for ${product}..."
    $DITA_OT \
        -input "src/${product}/maps/master.ditamap" \
        -format html5 \
        -output "output/html5/${product}" \
        -Dargs.input.dir="src/${product}" \
        -Dargs.output.base="output/html5/${product}"
done
```

---

# Publish Workflow: Multi-Product PDF

```bash
#!/bin/bash
# Stage 3: PDF generation across all products

PRODUCTS=("dr-5000" "dr-8000" "dr-2000")
DITA_OT="./dita-ot-4.0/bin/dita"
CUSTOM_DIR="../framework/custom-pdf"

echo "=== Stage 3: PDF Build ==="

for product in "${PRODUCTS[@]}"; do
    echo "Building PDF for ${product}..."
    $DITA_OT \
        -input "src/${product}/maps/master.ditamap" \
        -format pdf \
        -output "output/pdf/${product}" \
        -Dpdf.formatter=xep \
        -Dcustomization.dir="${CUSTOM_DIR}"
done
```

---

<!-- _class: tip -->

# Optimizing with Parallel Execution

Instead of running steps sequentially, use background processes:

```bash
#!/bin/bash
# Parallel build across products and formats

PRODUCTS=("dr-5000" "dr-8000" "dr-2000")
FORMATS=("html5" "pdf")
DITA_OT="./dita-ot-4.0/bin/dita"
MAX_PARALLEL=4
PID_COUNT=0

echo "=== Parallel Build ==="

for product in "${PRODUCTS[@]}"; do
    for format in "${FORMATS[@]}"; do
        echo "Starting $format for $product..."
        $DITA_OT \
            -input "src/${product}/maps/master.ditamap" \
            -format "${format}" \
            -output "output/${format}/${product}" &
        
        PID_COUNT=$((PID_COUNT + 1))
        
        # Throttle to max parallel
        if [ $PID_COUNT -ge $MAX_PARALLEL ]; then
            wait -n
            PID_COUNT=$((PID_COUNT - 1))
        fi
    done
done

# Wait for remaining builds
wait
echo "All builds completed"
```

This reduces sequential execution time by running parallel jobs. Set MAX_PARALLEL based on available build agent resources.

---

# Publish Workflow: Upload and Index

```bash
#!/bin/bash
# Stage 4: Deploy and index

echo "=== Stage 4: Deployment ==="

# Deploy to target (configure TARGET_URL, ACCESS_KEY, SECRET_KEY in CI/CD variables)
echo "Syncing HTML5 to ${TARGET_URL}..."
aws s3 sync output/html5/ "s3://${BUCKET_NAME}/" \
    --delete \
    --acl public-read \
    --cache-control "max-age=3600"

aws s3 sync output/pdf/ "s3://${BUCKET_NAME}/pdf/" \
    --delete \
    --acl public-read

# Index for search
echo "=== Stage 5: Search Indexing ==="
python scripts/index_to_elasticsearch.py \
    --source output/html5/ \
    --index docs-prod \
    --base-url "https://${DOCS_DOMAIN}"
echo "Search index updated"
```

---

# Publish Workflow: Notification

```bash
#!/bin/bash
# Stage 6: Team notification

SLACK_WEBHOOK_URL="${SLACK_WEBHOOK_URL:-}"
COMMIT_HASH=$(git rev-parse --short HEAD)

notify_success() {
    echo "=== Stage 6: Success Notification ==="
    echo "DITA documentation published successfully!"
    echo "Commit: ${COMMIT_HASH}"
    echo "Duration: < 1 hour"
    
    if [ -n "$SLACK_WEBHOOK_URL" ]; then
        curl -s -X POST -H "Content-type: application/json" \
            -d "{\"channel\":\"#docs-releases\",\"text\":\"DITA documentation published successfully!\\nCommit: ${COMMIT_HASH}\\nDuration: < 1 hour\"}" \
            "$SLACK_WEBHOOK_URL"
    fi
}

notify_failure() {
    echo "=== Stage 6: Failure Notification ==="
    echo "DITA publish FAILED!"
    echo "Commit: ${COMMIT_HASH}"
    
    if [ -n "$SLACK_WEBHOOK_URL" ]; then
        curl -s -X POST -H "Content-type: application/json" \
            -d "{\"channel\":\"#docs-alerts\",\"text\":\"DITA publish FAILED!\\nCommit: ${COMMIT_HASH}\"}" \
            "$SLACK_WEBHOOK_URL"
    fi
}

# Call appropriate function based on build status
# (CI/CD platform sets BUILD_STATUS variable)
if [ "${BUILD_STATUS}" = "success" ]; then
    notify_success
else
    notify_failure
fi
```

---

# Validate Workflow (validate.sh)

```bash
#!/bin/bash
# validate.sh - Run validation checks on DITA content
# Executed on pull request / merge request events

set -euo pipefail

echo "=== Validation Pipeline ==="

# XML Well-Formedness Check
echo "--- XML Well-Formedness Check ---"
find src/ -name "*.dita" -o -name "*.ditamap" | while read f; do
    xmllint --noout "$f" 2>&1 || { echo "FAIL: $f"; exit 1; }
done
echo "All XML files valid"
```

---

# Validate: Metadata Rule Enforcement

```bash
# --- Metadata Check ---
echo "--- M1-M7 Metadata Verification ---"
python3 scripts/check-metadata.py src/ --rules m1-m7
echo "Metadata check passed"
```

The `check-metadata.py` script enforces:
- **M1:** `docid` present and matches pattern `DOC-PROD-XXX-vN.N`
- **M2:** `product` and `product-version` present
- **M3:** `audience` contains valid value (admin|user|developer)
- **M4:** `status` is one of: draft|review|approved|obsolete
- **M5:** `security-level` is one of: public|internal|confidential
- **M6:** `author` and `reviewed-by` present
- **M7:** `created-date` and `revised-date` present and valid format

---

# Validate: Spell Check and Link Validation

```bash
# --- Spell Check ---
echo "--- Spell Check ---"
npm install -g cspell
cspell --config .cspell.json "src/**/*.dita"

# --- Link Validation ---
echo "--- Link Validation ---"
python3 scripts/check-links.py src/ \
    --check-xref \
    --check-conref \
    --check-keyref
```

---

# Validate: Subject Scheme and @class Verification

```bash
# --- Subject Scheme Validation ---
echo "--- Subject Scheme Validation ---"
./dita-ot-4.0/bin/dita \
    -input src/subject-scheme/subjectScheme.ditamap \
    -format subject-scheme-validate \
    -Dargs.input.dir=src/ \
    -Dargs.filter=src/subject-scheme/subjectSchemes.ditaval

# --- @class Attribute Verification ---
echo "--- @class Attribute Verification ---"
python3 scripts/check-class-attr.py src/ \
    --check-inheritance-chain \
    --report violations.csv
```

---

# Custom Validation Script: check-links.py

```yaml
# Framework CI/CD custom scripts

# scripts/check-links.py
```

```python
#!/usr/bin/env python3
"""Validate xref, conref, and keyref integrity in DITA sources."""
import os, sys, re, argparse
from lxml import etree

def check_xref_links(root_dir):
    """Verify every xref/@href resolves to an existing file."""
    violations = []
    for f in find_dita_files(root_dir):
        tree = etree.parse(f)
        for xref in tree.iter('xref'):
            href = xref.get('href')
            if href and not href.startswith('http'):
                target = resolve_path(f, href)
                if not os.path.exists(target):
                    violations.append((f, etree.tostring(xref)))
    return violations
```

---

# Custom Script: check-class-attr.py

```python
"""Verify @class attribute inheritance chain integrity."""
import os, re
from lxml import etree

# Known DITA class hierarchy
KNOWN_CHAINS = {
    'topic/p': '- topic/p',
    'topic/body': '- topic/body',
    'topic/section': '- topic/section',
    'concept/conbody': '- topic/body concept/conbody',
    'task/taskbody': '- topic/body task/taskbody',
    'ref/refbody': '- topic/body reference/refbody',
}

def check_class_attr(filepath):
    tree = etree.parse(filepath)
    violations = []
    for elem in tree.iter():
        cls = elem.get('class', '')
        if not cls:
            continue
        tag = etree.QName(elem).localname
        # Check: starts with '- '
        if not cls.startswith('- '):
            violations.append((filepath, tag, 'Missing leading "- "'))
        # Check: ends with space
        if not cls.endswith(' '):
            violations.append((filepath, tag, 'Missing trailing space'))
    return violations
```

---

# DITA-OT Customization: PDF Plugin

Custom PDF output for manufacturing documentation via XSL-FO:

```
framework/
└── custom-pdf/
    ├── plugin.xml              # DITA-OT plugin descriptor
    ├── integrator.xml          # Plugin integration points
    ├── build.xml               # Ant build file
    ├── cfg/
    │   ├── common/
    │   │   └── artwork/        # Manufacturing logos, icons
    │   ├── fo/
    │   │   ├── attrs/
    │   │   │   └── custom.xsl  # Override default FO attributes
    │   │   ├── xsl/
    │   │   │   └── custom.xsl  # Custom XSL transforms
    │   │   └── i18n/           # Localized static text
    │   └── axf/                # Antenna House specific config
    └── pdf-options.xml         # ExampleCorp PDF configuration
```

---

# DITA-OT Customization: HTML5 Plugin

```xml
<!-- framework/custom-html5/plugin.xml -->
<plugin id="com.dingrui.html5">
  <feature extension="dita.conductor.transtype.check"
           value="dingrui-html5"/>
  <feature extension="dita.transtype.print"
           value="dingrui-html5"/>

  <transtype name="dingrui-html5" desc="ExampleCorp Manufacturing HTML5">
    <feature extension="dita.conductor.target.relative"
             file="build_dingrui.xml"/>
  </transtype>
</plugin>
```

Key customizations:
- **Responsive navigation** with product switcher
- **Safety panel** highlighting hazard statements in red
- **Search integration** with Elasticsearch
- **PDF download** button per topic
- **ExampleCorp enterprise branding**

---

# Ant Build File for Custom Pipeline

```xml
<!-- build_complete.xml — Ant build for complex publishing -->
<project name="ExampleCorp Complete Build" default="publish-all">

  <property name="dita.dir" location="${basedir}/dita-ot-3.7"/>

  <target name="publish-all" depends="html5, pdf, epub"/>

  <target name="html5">
    <ant antfile="${dita.dir}/build.xml" target="init">
      <property name="args.input"
        value="${basedir}/src/${product}/maps/master.ditamap"/>
      <property name="transtype" value="dingrui-html5"/>
      <property name="output.dir"
        value="${basedir}/output/html5/${product}"/>
    </ant>
  </target>

  <target name="pdf">
    <ant antfile="${dita.dir}/build.xml" target="init">
      <property name="args.input"
        value="${basedir}/src/${product}/maps/master.ditamap"/>
      <property name="transtype" value="pdf"/>
      <property name="output.dir"
        value="${basedir}/output/pdf/${product}"/>
    </ant>
  </target>
</project>
```

---

# Deployment Targets

| Target | Protocol | Use Case |
|--------|----------|----------|
| **AWS S3 + CloudFront** | HTTPS | Primary public-facing documentation site |
| **Azure Static Web Apps** | HTTPS | Intranet/internal documentation (ISO compliance) |
| **Corporate Intranet** | HTTPS | Behind-firewall access for factory floor |
| **Web Server** | HTTPS | Development/staging previews per PR |

```bash
# Example: Deploy to Azure Static Web Apps (alternative to S3)
AZURE_TOKEN="${AZURE_TOKEN:?Missing Azure deployment token}"
az storage blob sync \
    --account-name docs-storage \
    --container '$web' \
    --source output/html5/ \
    --delete-destination true
```

---

# Monitoring Dashboard

Build status tracked through CI/CD platform dashboard plus custom monitoring:

```bash
#!/bin/bash
# monitor-build-health.sh - Calculate build metrics
# Run weekly (Monday 8 AM) via cron or CI/CD scheduler

LOG_FILE="ci-cd/build-history.log"

echo "=== Build Health Report ==="

if [ ! -f "$LOG_FILE" ]; then
    echo "No build history available"
    exit 0
fi

TOTAL=$(grep -c '.' "$LOG_FILE")
SUCCESS=$(grep -c "BUILD_SUCCESS" "$LOG_FILE")
FAILURE=$(grep -c "BUILD_FAILURE" "$LOG_FILE")

echo "Total builds: $TOTAL"
echo "Successful: $SUCCESS"
echo "Failed: $FAILURE"
echo "Success rate: $(echo "scale=1; $SUCCESS * 100 / $TOTAL" | bc)%"
```

---

# Publishing SLA

From the ExampleCorp governance charter, the CI/CD pipeline targets:

| Step | Target Time | Automated |
|------|-------------|-----------|
| Commit to main | Immediate | No (manual) |
| Trigger CI/CD | < 1 min | Yes |
| XML validation (validate workflow) | < 5 min | Yes |
| HTML5 generation (3 products) | < 15 min | Yes |
| PDF generation (3 products) | < 20 min | Yes |
| Upload to S3/Azure | < 5 min | Yes |
| Search indexing | < 10 min | Yes |
| **Total End-to-End** | **< 1 hour** | **95%** |

---

# Troubleshooting: DTD Resolution Errors

**Symptom:** `[DOTA001F] Failed to parse the DTD/XSD file`

**Root causes and fixes:**

| Cause | Fix |
|-------|-----|
| `catalog.xml` not in DITA-OT classpath | Set `--args.catalog=catalog.xml` or ensure it is in the root |
| PUBLIC ID mismatch between DOCTYPE and catalog | Verify exact string match (whitespace matters!) |
| Missing `.mod` or `.ent` file | Check all four specialization files exist |
| File path uses backslashes on Linux | Always use forward slashes in catalog paths |
| Custom DTD shell missing standard DITA module reference | Audit shell imports against base task.dtd |

---

# Troubleshooting: Memory and Timeout Issues

**Symptom:** CI/CD job killed after 120 minutes (PDF generation)

| Issue | Diagnosis | Fix |
|-------|-----------|-----|
| Large maps (> 500 topics) | PDF memory grows with topic count | Increase `timeout-minutes` to 240; use `max-parallel: 2` for PDF |
| Image resolution too high | Print-quality images at 600 DPI | Downsample to 150 DPI for web, 300 DPI for PDF |
| Rendered Audio/Video embeds | PDF formatter hangs on multimedia | Filter out `<object>` elements before PDF build |
| XEP/antenna house license | Proprietary formatter fails silently | Switch to `fop` as fallback: `-Dpdf.formatter=fop` |

```bash
./dita-ot-4.0/bin/dita \
    -input src/dr-5000/maps/master.ditamap \
    -format pdf \
    -Dpdf.formatter=fop \
    -Dprocessing-mode=strict
```

---

# Troubleshooting: Missing References

```bash
echo "=== Checking Image References ==="
python3 scripts/check-resources.py \
    --check-images \
    --check-conrefs \
    --check-xrefs \
    --source src/ \
    --report missing-refs.csv
```

Common causes:
- Image file not committed (in `.gitignore`)
- Typo in `@href` or `@conref` path
- Map references topic by wrong ID
- `@keyref` points to undefined key

---

<!-- _class: exercise -->

# Hands-On Exercise

**Goal:** Debug and fix a broken CI/CD pipeline.

**Scenario:** The MC-5000 publishing job fails with "DOTA001F" error.

**Steps:**
1. Examine the CI/CD build log to identify the failing step
2. Parse the error message: "publicId '-//ExampleCorp//DTD DITA Manufacturing Task//EN' not found"
3. Check `catalog.xml` for the correct PUBLIC ID mapping
4. Verify the DTD shell file exists at the mapped path
5. Fix the catalog entry and re-run the pipeline
6. Verify both HTML5 and PDF outputs are generated

**Deliverable:** A passing CI/CD run with correct output.

---

# Key Takeaways

| # | Takeaway |
|---|----------|
| 1 | CI/CD separates into **publish** (push to main) and **validate** (pull request) workflows |
| 2 | Matrix strategies reduce duplication; set `max-parallel` to avoid resource exhaustion |
| 3 | Custom validation scripts enforce M1-M7 metadata, @class integrity, and link health |
| 4 | DITA-OT plugins customize both HTML5 and PDF output for manufacturing branding |
| 5 | Troubleshooting CI/CD requires systematic analysis: DTD resolution, references, memory/timeout |
| 6 | Notifications (Slack/Teams) ensure team awareness of publish success and failures |
| 7 | SLA target: < 1 hour end-to-end from commit to search-indexed content |
