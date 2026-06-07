# DITA-OT Configuration Guide

**Document ID:** CIC-002  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved  

---

## Overview

This document covers DITA-OT installation options, plugin management, and build parameter configuration for manufacturing documentation.

---

## 1. Installation

### 1.1 Manual Installation

```bash
# Download DITA-OT 4.x
wget https://github.com/dita-ot/dita-ot/releases/download/4.0/dita-ot-4.0.zip
unzip dita-ot-4.0.zip -d /opt/
export PATH=/opt/dita-ot-4.0/bin:$PATH

# Verify installation
dita --version
```

### 1.2 Docker Installation

```bash
# Pull DITA-OT Docker image
docker pull ditaot/dita-ot:4.0

# Run build via Docker
docker run --rm \
  -v $(pwd)/src:/workspace/src \
  -v $(pwd)/output:/workspace/output \
  ditaot/dita-ot:4.0 \
  dita --input=/workspace/src/product/maps/master.ditamap \
       --format=html5 \
       --output=/workspace/output/html5
```

Docker is recommended for CI/CD environments to ensure reproducible builds.

---

## 2. Plugin Management

### 2.1 Installing Plugins

```bash
# List installed plugins
dita --plugins

# Install a plugin from a directory
dita --install /path/to/plugin

# Install from DITA-OT registry
dita --install com.elovirta.pdf
```

### 2.2 Recommended Plugins

| Plugin | Purpose | Installation |
|--------|---------|-------------|
| `org.dita.pdf2` | Standard PDF output | Bundled with DITA-OT |
| `com.elovirta.pdf` | PDF via CSS (Chemistry) | `dita --install com.elovirta.pdf` |
| `org.dita.html5` | HTML5 output | Bundled with DITA-OT |
| `org.dita.xhtml` | XHTML output | Bundled with DITA-OT |
| `org.dita.epub` | EPUB output | `dita --install org.dita.epub` |
| `com.oxygenxml.pdf.css` | CSS-based PDF (oXygen) | Via oXygen installation |

### 2.3 Custom Plugin Structure

```
framework/plugins/
└── com.example.manufacturing/
    ├── plugin.xml              # Plugin descriptor
    ├── integrator.xml          # Integration hooks
    ├── build.xml               # Ant build targets
    ├── cfg/
    │   ├── fo/
    │   │   ├── attrs/
    │   │   │   └── custom.xsl  # FO attribute overrides
    │   │   └── xsl/
    │   │       └── custom.xsl  # Custom XSL transforms
    │   └── html/
    │       └── css/
    │           └── custom.css  # Custom CSS for HTML5
    └── i18n/
        ├── messages_en.xml     # English messages
        └── messages_zh.xml     # Chinese messages
```

---

## 3. Build Parameters

### 3.1 Common Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `--input` | Input DITA map path | `src/product/maps/master.ditamap` |
| `--format` | Output format | `html5`, `pdf`, `epub` |
| `--output` | Output directory | `output/html5` |
| `--property` | DITA-OT property | `--property=args.css=style.css` |
| `--validate` | Enable validation | `--validate` |

### 3.2 Key Properties for Manufacturing Docs

```bash
# CSS customization
--property=args.css=framework/styles/shared/base.css

# Table of contents depth
--property=toc.section.depth=4

# Draft mode (shows draft comments)
--property=args.draft=false

# PDF formatter selection
--property=pdf.formatter=chemistry

# Image resolution
--property=args.image.resolution=300

# Copy output to base directory (for HTML5)
--property=args.copy.css=true

# Custom XSL directory
--property=args.xsl.dir=framework/custom-pdf/xsl
```

### 3.3 Ant Build Configuration

Create `build.xml` in the repository root:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="manufacturing-docs" default="build-all">
  <property name="dita.dir" value="/opt/dita-ot-4.0"/>
  <property name="src.dir" value="src"/>
  <property name="output.dir" value="output"/>
  
  <target name="build-all">
    <antcall target="build-product">
      <param name="product" value="product-a"/>
    </antcall>
    <antcall target="build-product">
      <param name="product" value="product-b"/>
    </antcall>
  </target>
  
  <target name="build-product">
    <ant antfile="${dita.dir}/build.xml">
      <property name="args.input"
        value="${src.dir}/${product}/maps/master.ditamap"/>
      <property name="transtype" value="html5"/>
      <property name="output.dir"
        value="${output.dir}/html5/${product}"/>
      <property name="args.css"
        value="framework/styles/shared/base.css"/>
    </ant>
  </target>
</project>
```

---

## 4. Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| `DOTA001F` | Missing DTD/XSD | Verify catalog.xml is in classpath |
| PDF generation fails | Missing PDF plugin | Install `com.elovirta.pdf` |
| HTML5 missing CSS | args.css path wrong | Use absolute path or correct relative path |
| Build timeout | Memory exhaustion | Increase JVM heap: `-Djava.awt.headless=true -Xmx2g` |
| Chinese characters garbled | Missing font | Add Chinese font to PDF plugin config |

---

## Related Documents

| Document | Location |
|----------|----------|
| CI/CD Pipeline | [01-CICD-PIPELINE.md](01-CICD-PIPELINE.md) |
| Publishing SLA | [03-PUBLISHING-SLA.md](03-PUBLISHING-SLA.md) |
| Cross-Format Consistency | [PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md](../PUBLISHING/01-CROSS-FORMAT-CONSISTENCY.md) |

---

**Document ID:** CIC-002  
**Next Review Date:** Q3 2026
