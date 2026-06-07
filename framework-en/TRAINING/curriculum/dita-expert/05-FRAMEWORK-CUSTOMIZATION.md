---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Framework Customization
## Extending DITA for Enterprise Manufacturing Systems

**DITA Expert Level - Module 5**
Example Manufacturing Co., Ltd.
v1.1 | 2026-06-07

---

<!-- _class: divider -->

# Agenda

1. Custom Topic Types
2. DITA-OT Plugin Development Basics
3. PDF Customization with XSL-FO
4. New Output Formats
5. Integrating with MES / PLM / ERP
6. Extending Subject Scheme
7. Custom Validation Rules
8. Exercise
9. Summary

---

<!-- _class: keypoint -->

# Learning Objectives

By the end of this module, you will be able to:

1. **Design and implement** a custom DITA topic type with DTD and XSD shells
2. **Create a DITA-OT plugin** for new output processing
3. **Customize PDF output** using XSL-FO for manufacturing documentation standards
4. **Integrate** DITA content pipelines with MES, PLM, and ERP systems
5. **Extend subject scheme** for controlled vocabulary enforcement

---

# Why Customize the Framework?

The standard DITA framework provides 80% of what you need. The remaining 20% requires customization:

| Need | Standard DITA | Custom Extension |
|------|--------------|-----------------|
| Manufacturing procedure | `<task>` | `<maintenanceTask>` with specialized attributes |
| Safety markup | Generic `<note>` | `<hazardstatement>` from hi-d domain |
| ERP integration | N/A | Custom output plugin for SAP/Oracle |
| MES machine data | N/A | API integration for real-time machine specs |
| ISO compliance checking | N/A | Custom Schematron rules |

---

# Part 1: Custom Topic Types

A custom topic type extends a base DITA type with domain-specific structure and attributes.

**Use case:** A `maintenanceTask` type that inherits from `<task>` but adds mandatory safety prerequisites and equipment checklists.

```
Base: <task>
  → Custom: <maintenanceTask>
      Required: <safetyPrereq> (from pr-d domain)
      Required: <equipmentChecklist> (from custom maint-e domain)
      Optional: <calibrationRef> (from custom cal-d domain)
```

```
Inheritance chain:
topic → topic/task → topic/task/maintenanceTask
```

---

# Custom Topic Type: DTD Declaration

```xml
<!-- maintenanceTask.mod -->
<!-- Custom Maintenance Task Topic Type Module -->

<!-- ============================================ -->
<!-- ELEMENT NAME ENTITIES -->
<!-- ============================================ -->
<!ENTITY % maintenanceTask "maintenanceTask">
<!ENTITY % maintTaskBody "maintTaskBody">
<!ENTITY % maintPreReq "maintPreReq">
<!ENTITY % equipmentChecklist "equipmentChecklist">
<!ENTITY % lockoutProcedure "lockoutProcedure">

<!-- ============================================ -->
<!-- ELEMENT DECLARATIONS -->
<!-- ============================================ -->
<!ELEMENT maintenanceTask
  ((%title;), (%titlealts;)?, (%shortdesc;)?,
   (%prolog;)?, (%maintPreReq;)?, (%maintTaskBody;)?,
   (%related-links;)?, (%maintenanceTask-info-types;)*)
>
<!ATTLIST maintenanceTask
  %id-atts;
  %localization-atts;
  class CDATA "- topic/task maintenanceTask/maintTaskBody ">
  DTDVersion CDATA #FIXED "1.1"
  domains CDATA "&included-domains;"
>
```

---

# Custom Topic Type: Content Model

```xml
<!-- maintTaskBody content model -->
<!ELEMENT maintTaskBody
  ((%equipmentChecklist;)?, (%lockoutProcedure;)?,
   (%prereq;)?, (%context;)?, (%steps;)?,
   (%result;)?, (%example;)?, (%postreq;)?)
>
<!ATTLIST maintTaskBody
  %id-atts;
  %localization-atts;
  class CDATA "- topic/body task/taskbody
               maintenanceTask/maintTaskBody ">
>

<!-- Equipment checklist: list of required tools/equipment -->
<!ELEMENT equipmentChecklist
  ((%equipmentItem;)+)
>
<!ATTLIST equipmentChecklist
  %id-atts;
  class CDATA "- topic/ul maintenanceTask/equipmentChecklist ">
>

<!ELEMENT equipmentItem (%ph;)*>
<!ATTLIST equipmentItem
  %id-atts;
  partNumber CDATA #IMPLIED
  quantity    CDATA #IMPLIED
  class CDATA "- topic/li maintenanceTask/equipmentItem ">
>
```

---

# Custom Topic Type: Shell DTD

Integrate the new topic type into a product-specific shell:

```xml
<!-- mfg-full.dtd — Full Manufacturing DTD Shell -->
<!-- Integrates: base DITA + hi-d + pr-d + maint-d + maintTask -->

<!-- 1. Base DITA topic entities -->
<!ENTITY % topic-type
  PUBLIC "-//OASIS//ELEMENTS DITA Topic//EN" "topic.mod">
%topic-type;

<!-- 2. Task specialization -->
<!ENTITY % task-typemod
  PUBLIC "-//OASIS//ELEMENTS DITA Task//EN" "task.mod">
%task-typemod;

<!-- 3. Custom maintenance task -->
<!ENTITY % maintenanceTask-typemod
  PUBLIC "-//ExampleCorp//ELEMENTS DITA MaintenanceTask//EN"
         "maintenanceTask.mod">
%maintenanceTask-typemod;

<!-- 4. Domain integrations -->
<!ENTITY % hi-d-dec
  PUBLIC "-//OASIS//ENTITIES DITA Hazard Statement Domain//EN"
         "hazardStatementDomain.ent">
%hi-d-dec;
<!-- ... additional domain integrations ... -->
```

---

# Custom Topic Type: Topic Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE maintenanceTask
  PUBLIC "-//ExampleCorp//DTD DITA MaintenanceTask//EN"
  "mfg-full.dtd">
<maintenanceTask id="mt_spindle_bearing_replacement">
  <title>Replace MC-5000 Spindle Bearings</title>
  <shortdesc>
    Complete procedure for replacing angular contact spindle
    bearings on the MC-5000 CNC machining center.
  </shortdesc>

  <maintPreReq>
    <personnel>
      <role>CNC Service Technician Level 3</role>
      <certification>ISO 9001 Calibration Certified</certification>
    </personnel>
    <safety>
      <noqual>
        Must lock out and tag out main power before procedure.
      </noqual>
    </safety>
  </maintPreReq>

  <maintTaskBody>
    <equipmentChecklist>
      <equipmentItem partNumber="TL-4500" quantity="1">
        Torque wrench (20-100 Nm calibrated)
      </equipmentItem>
      <equipmentItem partNumber="PB-7890" quantity="2">
        Angular contact bearing set (NSK 7014CTYNSULP4)
      </equipmentItem>
      <equipmentItem partNumber="LK-2200" quantity="1">
        Klu00fcber Isoflex NBU 15 grease (50g tube)
      </equipmentItem>
    </equipmentChecklist>

    <lockoutProcedure>
      <steps>
        <step><cmd>Power off main disconnect switch.</cmd></step>
        <step><cmd>Apply lock and tag to main disconnect.</cmd></step>
        <step><cmd>Verify zero energy state at spindle drive.</cmd></step>
      </steps>
    </lockoutProcedure>

    <steps>
      <step><cmd>Remove spindle nose cap.</cmd></step>
      <step><cmd>Remove drawbar assembly.</cmd>
        <info>Use drawbar removal tool TL-4500.</info>
      </step>
      <!-- ... additional steps ... -->
    </steps>
  </maintTaskBody>
</maintenanceTask>
```

---

# Custom Topic Type: oXygen Integration

To make the custom topic type available in oXygen's New Document wizard:

```xml
<!-- oxygen-maintenanceTask-template.xml -->
<!-- Place in: $OXYGEN_INSTALL_DIR/frameworks/dita/templates/ -->
<templateDescriptor>
  <name>Maintenance Task (ExampleCorp Manufacturing)</name>
  <description>
    Creates a maintenance task topic with safety prerequisites,
    equipment checklist, and lockout procedure sections.
  </description>
  <documentType>DITA</documentType>
  <customClass>ro.sync.dita.maintTaskTemplate</customClass>
  <icon>maintenance-task.png</icon>
</templateDescriptor>
```

Plus, add the new DTD to oXygen's DITA document type association so validation and content completion recognize `maintenanceTask` elements.

---

# Part 2: DITA-OT Plugin Development

**DITA-OT Plugin Architecture:**

```
com.dingrui.mfg-pdf/
├── plugin.xml              # Plugin descriptor (REQUIRED)
├── build.xml               # Ant build integration
├── integrator.xml           # Plugin extension points
├── cfg/
│   ├── fo/
│   │   ├── attrs/
│   │   │   ├── basic-settings.xsl
│   │   │   └── custom.xsl
│   │   ├── xsl/
│   │   │   ├── custom.xsl
│   │   │   └── hazardstatement.xsl
│   │   └── i18n/
│   │       ├── zh-CN.xml
│   │       └── de-DE.xml
│   └── common/
│       └── artwork/
│           └── dingrui-logo.svg
└── pdf-options.xml
```

---

# DITA-OT Plugin: plugin.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<plugin id="com.dingrui.mfg-pdf">
  <feature extension="dita.conductor.transtype.check"
           value="dingrui-pdf"/>
  <feature extension="dita.conductor.target.relative"
           file="build.xml"/>
  <feature extension="dita.transtype.print" value="dingrui-pdf"/>

  <!-- Register custom PDF transformation -->
  <transtype name="dingrui-pdf"
             desc="ExampleCorp Manufacturing PDF with Safety Markup">
    <feature extension="dita.conductor.target.relative"
             file="build.xml"/>
  </transtype>
</plugin>
```

---

# DITA-OT Plugin: build.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="com.dingrui.mfg-pdf">
  <property name="customization.dir"
    value="${dita.plugin.com.dingrui.mfg-pdf.dir}/cfg"/>

  <!-- Call the base PDF transformation with customization -->
  <target name="dita2dingrui-pdf"
          depends="dita2dingrui-pdf.init,
                   dita2pdf"/>
  <target name="dita2dingrui-pdf.init">
    <property name="transtype" value="pdf"/>
    <property name="pdf.formatter" value="ah"/>
    <property name="customization.dir"
      value="${dita.plugin.com.dingrui.mfg-pdf.dir}/cfg"/>
    <property name="args.figurelink.style" value="NUMBERTITLE"/>
    <property name="args.tablelink.style" value="NUMBERTITLE"/>
    <!-- Manufacturing-specific properties -->
    <property name="mfg.safety.color" value="#FF0000"/>
    <property name="mfg.show-part-numbers" value="true"/>
  </target>
</project>
```

---

# DITA-OT Plugin: integrator.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<plugin id="com.dingrui.mfg-pdf">
  <!-- Register as an extension of the base PDF plugin -->
  <require plugin="org.dita.pdf2"/>
  <require plugin="org.dita.pdf2.fop"/>

  <!-- Extend the XSLT import chain for FO customization -->
  <feature extension="dita.xsl.fo" file="cfg/fo/xsl/custom.xsl"/>

  <!-- Extend the attribute set customization -->
  <feature extension="dita.xsl.fo.attrs"
           file="cfg/fo/attrs/custom.xsl"/>

  <!-- Add i18n variables for Chinese and German -->
  <feature extension="dita.xsl.strings"
           file="cfg/fo/i18n/zh-CN.xml"/>
  <feature extension="dita.xsl.strings"
           file="cfg/fo/i18n/de-DE.xml"/>

  <!-- Custom processing for manufacturing domains -->
  <feature extension="dita.conductor.lib.import"
           file="lib/mfg-utils.jar"/>
</plugin>
```

---

# Part 3: PDF Customization with XSL-FO

**Why custom PDF?** Manufacturing documentation has specific PDF requirements:

- Safety warnings must be visually prominent (red border, icon)
- Part numbers must be rendered in monospace font with barcode
- Equipment checklists must appear as structured tables
- Lockout/tagout procedures must have step-numbered callouts
- Every page must carry the document control number and revision

XSL-FO is the intermediate format between DITA XML and the final PDF:

```
DITA XML  →  XSLT  →  XSL-FO  →  PDF Formatter (AH/FOP)  →  PDF
```

---

# XSL-FO: Hazard Statement Customization

```xml
<!-- cfg/fo/xsl/hazardstatement.xsl -->
<xsl:stylesheet version="2.0"
  xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
  xmlns:fo="http://www.w3.org/1999/XSL/Format">

  <!-- Override hazardstatement template -->
  <xsl:template match="*[contains(@class, ' topic/hazardstatement ')]">
    <fo:block-container
      border="2pt solid red"
      background-color="#FFF0F0"
      padding="8pt"
      margin-top="12pt"
      margin-bottom="12pt"
      keep-together.within-page="always">

      <!-- Safety icon -->
      <fo:external-graphic
        src="url({concat($artwork-path, '/safety-warning.svg')})"
        width="24pt" height="24pt"
        alignment-adjust="middle"/>

      <!-- Signal word (DANGER, WARNING, CAUTION) -->
      <fo:block font-size="14pt" font-weight="bold"
                color="red" margin-top="4pt">
        <xsl:value-of select="upper-case(@type)"/>
      </fo:block>

      <!-- Hazard content -->
      <xsl:apply-templates/>
    </fo:block-container>
  </xsl:template>
</xsl:stylesheet>
```

---

# XSL-FO: Part Number Table Customization

```xml
<!-- cfg/fo/xsl/equipment-checklist.xsl -->
<xsl:template match="*[contains(@class, ' maintenanceTask/equipmentChecklist ')]">
  <fo:block font-size="12pt" font-weight="bold"
            margin-top="12pt" margin-bottom="6pt">
    Required Equipment and Tools
  </fo:block>

  <fo:table table-layout="fixed" width="100%"
            border="1pt solid #333333">
    <fo:table-column column-width="proportional-column-width(1)"/>
    <fo:table-column column-width="proportional-column-width(2)"/>
    <fo:table-column column-width="proportional-column-width(1)"/>

    <fo:table-header>
      <fo:table-row background-color="#E0E0E0">
        <fo:table-cell padding="4pt">
          <fo:block font-weight="bold">Part Number</fo:block>
        </fo:table-cell>
        <fo:table-cell padding="4pt">
          <fo:block font-weight="bold">Description</fo:block>
        </fo:table-cell>
        <fo:table-cell padding="4pt">
          <fo:block font-weight="bold">Quantity</fo:block>
        </fo:table-cell>
      </fo:table-row>
    </fo:table-header>

    <fo:table-body>
      <xsl:apply-templates select="*[contains(@class, ' maintenanceTask/equipmentItem ')]"/>
    </fo:table-body>
  </fo:table>
</xsl:template>
```

---

# XSL-FO: Page Headers and Footers

```xml
<!-- cfg/fo/xsl/page-masters.xsl -->
<xsl:template name="insertBodyStaticContents">
  <!-- Running header: product name + document title -->
  <fo:static-content flow-name="odd-page-header">
    <fo:block font-size="8pt" color="#666666"
              text-align="left" border-bottom="0.5pt solid #CCCCCC">
      <xsl:value-of select="//bookmeta/prodname"/>
      <xsl:text> | </xsl:text>
      <xsl:value-of select="//bookmeta/booktitlealt"/>
    </fo:block>
  </fo:static-content>

  <!-- Running footer: document control number, revision, page # -->
  <fo:static-content flow-name="odd-page-footer">
    <fo:block font-size="7pt" color="#999999"
              text-align="center" border-top="0.5pt solid #CCCCCC">
      <xsl:text>DOC-CTRL-</xsl:text>
      <xsl:value-of select="//bookmeta/docid"/>
      <xsl:text> | Rev </xsl:text>
      <xsl:value-of select="//bookmeta/bookversion"/>
      <xsl:text> | Page </xsl:text>
      <fo:page-number/>
    </fo:block>
  </fo:static-content>
</xsl:template>
```

---

# PDF Customization: Front Matter

Manufacturing manuals need specific front matter:

```xml
<!-- cfg/fo/xsl/front-matter.xsl -->
<xsl:template name="createFrontMatter">
  <fo:page-sequence master-reference="front-matter">

    <!-- Cover page -->
    <fo:flow flow-name="xsl-region-body">
      <!-- Product image -->
      <fo:external-graphic
        src="url({concat($artwork-path, '/dr-5000-hero.jpg')})"
        width="100%" content-width="scale-to-fit"/>

      <fo:block font-size="24pt" font-weight="bold"
                margin-top="24pt" text-align="center">
        <xsl:value-of select="//bookmeta/booktitlealt"/>
      </fo:block>

      <fo:block font-size="14pt" margin-top="12pt" text-align="center">
        Document ID: <xsl:value-of select="//bookmeta/docid"/>
      </fo:block>

      <fo:block font-size="12pt" margin-top="6pt" text-align="center">
        Revision <xsl:value-of select="//bookmeta/bookversion"/>
        | <xsl:value-of select="//bookmeta/publishdate"/>
      </fo:block>

      <!-- Proprietary notice -->
      <fo:block font-size="9pt" color="#666666"
                margin-top="48pt" text-align="center">
        Confidential — Example Manufacturing Co., Ltd.
        Reproduction prohibited without written authorization.
      </fo:block>
    </fo:flow>
  </fo:page-sequence>
</xsl:template>
```

---

# PDF Customization: ISO Compliance Footer

```xml
<!-- ISO 9001 / ISO 12100 required documentation markings -->
<xsl:template name="insertISOFooter">
  <fo:static-content flow-name="odd-page-footer">
    <fo:block font-size="6.5pt" color="#888888"
              text-align="left" border-top="0.5pt solid #CCCCCC"
              padding-top="2pt">

      <!-- Left: Document control info -->
      <fo:inline text-align="left">
        <xsl:value-of select="//bookmeta/docid"/>
        <xsl:text> Rev </xsl:text>
        <xsl:value-of select="//bookmeta/bookversion"/>
      </fo:inline>

      <!-- Center: ISO compliance statement -->
      <fo:inline text-align="center">
        <xsl:text>Controlled Document — ISO 9001:2015 / ISO 12100:2010</xsl:text>
      </fo:inline>

      <!-- Right: Page number and security level -->
      <fo:inline text-align="right">
        <xsl:value-of select="//bookmeta/security-level"/>
        <xsl:text> | Page </xsl:text>
        <fo:page-number/>
        <xsl:text> of </xsl:text>
        <fo:page-number-citation ref-id="last-page"/>
      </fo:inline>
    </fo:block>
  </fo:static-content>
</xsl:template>
```

---

# Part 4: New Output Formats

Beyond HTML5 and PDF, manufacturing needs specialized output formats:

| Format | Use Case | Technology |
|--------|----------|-----------|
| **SCORM/eLearning** | Operator training modules | DITA-OT SCORM plugin + custom XSLT |
| **ERP Bill of Materials** | Sync documentation to SAP part records | Custom JSON/XML output plugin |
| **MES Work Instructions** | Display procedures at machine terminals | HTML fragment + REST API |
| **Interactive SVG** | Exploded parts diagrams with hotspots | DITA-OT + custom JavaScript |
| **EPUB** | Field service tablets (offline) | Standard DITA-OT EPUB + branding |
| **IIoT Data Feed** | Machine-readable safety and maintenance data | Custom MQTT/OPC UA plugin |

---

# New Output Format: MES Work Instructions

MES (Manufacturing Execution System) terminals on the factory floor display work instructions. We need a lightweight HTML fragment format:

```xml
<!-- plugin-mes.xml -->
<plugin id="com.dingrui.mes-output">
  <feature extension="dita.conductor.transtype.check"
           value="mes-work-instruction"/>
  <feature extension="dita.conductor.target.relative"
           file="build_mes.xml"/>
  <transtype name="mes-work-instruction"
             desc="MES Work Instruction (HTML fragment)">
    <feature extension="dita.conductor.target.relative"
             file="build_mes.xml"/>
  </transtype>
</plugin>
```

```xml
<!-- build_mes.xml excerpt -->
<target name="dita2mes-work-instruction">
  <xslt basedir="${input.dir}" destdir="${output.dir}"
        style="${plugin.dir}/xsl/mes-transform.xsl"
        includes="**/*.dita">
    <param name="MES-ENDPOINT"
           expression="https://mes.dingrui.com/api/v2/work-instructions"/>
    <param name="MACHINE-ID" expression="${machine.id}"/>
  </xslt>
</target>
```

---

# MES Output: XSLT Transform

```xml
<!-- xsl/mes-transform.xsl — DITA to MES HTML fragment -->
<xsl:stylesheet version="2.0"
  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

  <xsl:output method="html" indent="yes"/>

  <!-- Only process task topics for MES -->
  <xsl:template match="/">
    <div class="mes-work-instruction"
         data-machine-id="{$MACHINE-ID}"
         data-topic-id="{/*/@id}">

      <h2><xsl:value-of select="/*/title"/></h2>

      <!-- Safety warnings highlighted for MES display -->
      <xsl:apply-templates
        select="//*[contains(@class, ' topic/hazardstatement ')]"
        mode="mes-safety"/>

      <!-- Steps with large touchscreen-friendly numbering -->
      <ol class="mes-steps">
        <xsl:apply-templates select="//step/cmd"/>
      </ol>
    </div>
  </xsl:template>
</xsl:stylesheet>
```

---

# Part 5: Integrating with MES / PLM / ERP

```
┌──────────────────────────────────────────────────────────────┐
│                    ENTERPRISE INTEGRATION                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐               │
│  │   PLM    │    │   DITA   │    │   MES    │               │
│  │ (Team-   │───▶│ Content  │───▶│ (Factory │               │
│  │  center) │    │ Pipeline │    │  Floor)  │               │
│  └──────────┘    └────┬─────┘    └──────────┘               │
│                       │                                      │
│                       ├──▶ ┌──────────┐  PDF to document     │
│                       │    │    ERP    │  control system      │
│                       │    │  (SAP)    │                      │
│                       │    └──────────┘                      │
│                       │                                      │
│                       └──▶ ┌──────────┐  Service bulletins   │
│                            │   CRM    │  and field updates   │
│                            │(Salesfrce)│                      │
│                            └──────────┘                      │
└──────────────────────────────────────────────────────────────┘
```

---

# PLM Integration: Bill of Materials Sync

The PLM system (Teamcenter) is the source of truth for product BOM. We must keep documentation synchronized with the BOM.

```python
#!/usr/bin/env python3
"""Sync PLM BOM data to DITA reference topics."""
import requests, xml.etree.ElementTree as ET

PLM_API = "https://plm.dingrui.com/api/v2/bom"
DITA_SRC = "src/dr-5000/reference/bom/"

def sync_bom_to_dita(product_id="MC-5000"):
    """Pull BOM from PLM, generate DITA reference topics."""

    # 1. Fetch BOM from Teamcenter
    resp = requests.get(
        f"{PLM_API}/{product_id}",
        headers={"Authorization": f"Bearer {PLM_TOKEN}"})
    bom = resp.json()

    # 2. Generate one DITA reference topic per subassembly
    for assembly in bom['assemblies']:
        topic = ET.Element("reference", id=f"bom_{assembly['id']}")
        ET.SubElement(topic, "title").text = assembly['name']
        refbody = ET.SubElement(topic, "refbody")

        # Create a properties table for BOM attributes
        table = ET.SubElement(refbody, "table")
        tgroup = ET.SubElement(table, "tgroup", cols="3")
        thead = ET.SubElement(tgroup, "thead")
        # ... populate with part_number, description, quantity, material

        # Write to file
        tree = ET.ElementTree(topic)
        tree.write(f"{DITA_SRC}/bom_{assembly['id']}.dita",
                   encoding="utf-8", xml_declaration=True)
        print(f"  Generated: bom_{assembly['id']}.dita")

sync_bom_to_dita()
```

---

# ERP Integration: SAP Output Plugin

Manufacturing ERP systems consume documentation for work order attachments and compliance records:

```python
"""DITA-OT post-processing: push PDF packages to SAP DMS."""
import requests, os, glob

SAP_DMS_API = "https://sap.dingrui.com/sap/opu/odata/sap/DMS_DOCUMENT_SRV"

def push_to_sap_dms(pdf_dir, product_id):
    """Upload generated PDFs to SAP Document Management System."""

    for pdf_file in glob.glob(f"{pdf_dir}/*.pdf"):
        filename = os.path.basename(pdf_file)

        # Extract doc ID from filename: MC5000-OP-MAN-v2.3.pdf
        doc_id = filename.replace('.pdf', '')

        with open(pdf_file, 'rb') as f:
            response = requests.post(
                f"{SAP_DMS_API}/DocumentSet",
                headers={
                    "Content-Type": "application/pdf",
                    "x-csrf-token": CSRF_TOKEN,
                },
                data=f.read(),
                params={
                    "DocumentNumber": doc_id,
                    "DocumentType": "MAN",
                    "ProductID": product_id,
                })
            print(f"  SAP DMS: {doc_id} → HTTP {response.status_code}")

# Run as part of CI/CD publish workflow:
# python scripts/push_to_sap.py output/pdf/dr-5000 MC-5000
```

---

# MES Integration: Real-Time Work Instructions

Factory floor terminals call the MES API to retrieve work instructions on demand:

```python
"""MES API endpoint: serve DITA-based work instructions to floor terminals."""
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/api/v1/work-instruction/<machine_id>/<procedure_id>')
def get_work_instruction(machine_id, procedure_id):
    """Return the work instruction for a specific machine and procedure."""

    # Look up the DITA topic mapped to this machine+procedure
    topic_path = resolve_topic_path(machine_id, procedure_id)

    # Transform DITA to HTML fragment (cached)
    html = transform_dita_to_mes(topic_path)

    return jsonify({
        "machine_id": machine_id,
        "procedure_id": procedure_id,
        "instruction_html": html,
        "hazard_warnings": extract_hazards(topic_path),
        "required_tools": extract_equipment(topic_path),
        "estimated_minutes": extract_duration(topic_path),
    })

# Factory floor terminal calls:
# GET /api/v1/work-instruction/MC5000-042/spindle-bearing-replace
```

---

# Part 6: Extending Subject Scheme

Subject scheme enforces controlled vocabulary. In manufacturing, we extend it to cover:

- Product feature taxonomies
- Safety hazard classifications
- ISO compliance checklists
- Component lifecycle states

```xml
<!-- subjectScheme.ditamap — Extended Manufacturing Subject Scheme -->
<subjectScheme>
  <!-- Product taxonomy -->
  <subjectdef keys="products" navtitle="Product Lines">
    <subjectdef keys="dr-2000" navtitle="DR-2000">
      <subjectdef keys="dr2000_v1" navtitle="DR-2000 v1"/>
      <subjectdef keys="dr2000_v2" navtitle="DR-2000 v2"/>
    </subjectdef>
    <subjectdef keys="dr-5000" navtitle="MC-5000">
      <subjectdef keys="dr5000_v1" navtitle="MC-5000 v1"/>
      <subjectdef keys="dr5000_v2" navtitle="MC-5000 v2"/>
    </subjectdef>
    <subjectdef keys="dr-8000" navtitle="MC-8000">
      <subjectdef keys="dr8000_v1" navtitle="MC-8000 v1"/>
    </subjectdef>
  </subjectdef>

  <!-- Hazard classification taxonomy -->
  <subjectdef keys="hazard_types" navtitle="Hazard Classifications">
    <subjectdef keys="hazard_mechanical" navtitle="Mechanical Hazards">
      <subjectdef keys="hazard_crush" navtitle="Crushing"/>
      <subjectdef keys="hazard_entangle" navtitle="Entanglement"/>
      <subjectdef keys="hazard_cut" navtitle="Cutting/Severing"/>
    </subjectdef>
    <subjectdef keys="hazard_electrical" navtitle="Electrical Hazards"/>
    <subjectdef keys="hazard_thermal" navtitle="Thermal Hazards"/>
    <subjectdef keys="hazard_chemical" navtitle="Chemical Hazards"/>
  </subjectdef>
</subjectScheme>
```

---

# Subject Scheme: Enumerated Value Enforcement

```xml
<!-- Enforce that @product attribute values match defined product keys -->
<enumerationdef>
  <attributedef name="product"/>
  <subjectdef keyref="products"/>
</enumerationdef>

<!-- Enforce that @hazard_type attribute matches defined hazard keys -->
<enumerationdef>
  <attributedef name="hazard_type"/>
  <subjectdef keyref="hazard_types"/>
</enumerationdef>

<!-- Enforce documentation status lifecycle -->
<enumerationdef>
  <attributedef name="status"/>
  <subjectdef keyref="doc_statuses"/>
</enumerationdef>

<subjectdef keys="doc_statuses">
  <subjectdef keys="status_draft"/>
  <subjectdef keys="status_review"/>
  <subjectdef keys="status_approved"/>
  <subjectdef keys="status_published"/>
  <subjectdef keys="status_obsolete"/>
</subjectdef>
```

---

# Part 7: Custom Validation Rules (Schematron)

Beyond DTD validation, Schematron provides business-rule validation:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://purl.oclc.org/dsdl/schematron">

  <pattern id="safety-rules">
    <title>Safety Content Requirements</title>

    <!-- Rule 1: Every maintenance task must have hazard statements -->
    <rule context="*[contains(@class, ' maintenanceTask/maintTaskBody ')]">
      <assert test="preceding::*[contains(@class, ' topic/hazardstatement ')]"
        id="SAF-001" role="error">
        Maintenance task is missing a hazard statement. Every maintenance
        procedure MUST declare associated hazards per ISO 12100.
      </assert>
    </rule>

    <!-- Rule 2: Hazard statement must have type attribute -->
    <rule context="*[contains(@class, ' topic/hazardstatement ')]">
      <assert test="@type"
        id="SAF-002" role="error">
        Hazard statement must specify a type (danger, warning, or caution).
      </assert>
    </rule>

    <!-- Rule 3: Lockout procedure must have at least 3 steps -->
    <rule context="maintenanceTask/maintTaskBody/lockoutProcedure">
      <assert test="count(steps/step) >= 3"
        id="SAF-003" role="warning">
        Lockout procedure should contain at least 3 steps
        (disconnect, lock, verify).
      </assert>
    </rule>
  </pattern>

  <!-- Additional patterns for metadata, ISO compliance, etc. -->
</schema>
```

---

# Custom Validation: Integrating Schematron into CI/CD

```yaml
# Add Schematron validation step to validate.yml
schematron-check:
  runs-on: ubuntu-latest
  steps:
  - uses: actions/checkout@v3

  - name: Install Schematron processor
    run: |
      pip install lxml
      wget https://github.com/Schematron/schematron/releases/download/...

  - name: Run Schematron validation
    run: |
      echo "Running manufacturing safety Schematron rules..."
      for f in $(find src/ -name "*.dita"); do
        result=$(schematron-validate \
          --schema framework/schematron/mfg-safety.sch \
          --input "$f" 2>&1)
        if [ -n "$result" ]; then
          echo "VIOLATION: $f"
          echo "$result"
          VIOLATIONS=$((VIOLATIONS + 1))
        fi
      done
      if [ $VIOLATIONS -gt 0 ]; then
        echo "ERROR: $VIOLATIONS Schematron violations found"
        exit 1
      fi
```

---

# Framework Customization: Testing Strategy

Every customization must be tested before deployment:

| Test Type | What to Verify | Tool |
|-----------|---------------|------|
| **Unit test** | Individual XSLT template produces correct output | Saxon + XSpec |
| **Integration test** | Custom DTD validates against sample topics | xmllint |
| **Regression test** | Existing topics still build after DTD/XSL change | DITA-OT batch build |
| **Visual regression** | PDF output matches reference screenshots | ImageMagick compare |
| **Performance test** | Build time does not regress beyond 20% | GitHub Actions timing |

```python
"""Example: Integration test for custom maintenanceTask DTD."""
import subprocess

def test_maintenance_task_validation():
    """Verify maintenanceTask topics validate against mfg-full.dtd."""
    result = subprocess.run([
        'xmllint', '--dtdvalid', 'framework/dtd/mfg-full.dtd',
        '--noout', 'src/test/samples/maintenance-task-valid.dita'
    ], capture_output=True, text=True)
    assert result.returncode == 0, f"Validation failed: {result.stderr}"
```

---

# Customization Rollout Process

```yaml
rollout_process:
  phase_1_development:
    duration: "2-4 weeks"
    activities:
      - "Develop custom DTD/XSD specialization"
      - "Create DITA-OT plugin skeleton"
      - "Implement XSL-FO customizations"
      - "Write unit tests (XSpec)"
      - "Deploy to staging environment"

  phase_2_pilot:
    duration: "2 weeks"
    activities:
      - "Select 15-20 representative topics for pilot"
      - "Writers test new topic type with pilot content"
      - "Validate output across HTML5, PDF, MES formats"
      - "Gather writer feedback on usability"
      - "Fix issues found during pilot"

  phase_3_rollout:
    duration: "4 weeks"
    activities:
      - "Train all writers on new topic type/plugin"
      - "Update oXygen templates and content completion"
      - "Release new DTD shell to product teams"
      - "Migrate existing content to new topic type (if applicable)"
      - "Enable Schematron rules in CI/CD"

  phase_4_support:
    duration: "Ongoing"
    activities:
      - "Monitor CI/CD for customization-related failures"
      - "Address writer questions in weekly office hours"
      - "Release patch versions as needed"
```

---

<!-- _class: exercise -->

# Hands-On Exercise

**Goal:** Extend the DITA framework for a new manufacturing domain and create a custom output format.

**Scenario:** ExampleCorp needs a "Quality Inspection" domain for documenting CMM (Coordinate Measuring Machine) inspection procedures with a custom output for the quality lab tablet.

**Steps:**

1. **Create the domain specialization** (`inspectionDomain.mod` and `inspectionDomain.ent`):
   - `<inspectionProcedure>` (extends `<task>`)
   - `<measurementPoint>` with attributes: `nominal`, `tolerance-plus`, `tolerance-minus`, `unit`
   - `<inspectionResult>` with attribute: `pass` (true/false)

2. **Create the DTD shell** (`mfg-inspection.dtd`) integrating the inspection domain with base DITA and hi-d.

3. **Create a DITA-OT plugin** (`com.dingrui.inspection-report`) that generates an HTML inspection report with pass/fail color coding.

4. **Write a Schematron rule** that requires every `<inspectionProcedure>` to have at least one `<measurementPoint>`.

5. **Create a sample topic** and test the full pipeline: XML validation, DITA-OT transform, output inspection.

**Deliverable:** Working plugin directory with sample output.

---

# Key Takeaways

| # | Takeaway |
|---|----------|
| 1 | Custom topic types extend base DITA for domain-specific structure -- always preserve the `@class` inheritance chain |
| 2 | DITA-OT plugins use `plugin.xml`, `build.xml`, and `integrator.xml` to register new transforms |
| 3 | XSL-FO customization is the gateway to manufacturing-grade PDF with safety markup, part tables, and ISO compliance |
| 4 | New output formats (MES, ERP, SCORM) extend DITA's reach beyond traditional documentation |
| 5 | Enterprise integration (PLM/ERP/MES) transforms DITA from a publishing tool into a data hub |
| 6 | Subject scheme extension creates enforceable controlled vocabularies for products, hazards, and statuses |
| 7 | Schematron rules enforce business logic that DTD/XSD cannot express |
| 8 | Every customization must include a testing strategy (unit, integration, regression) and a phased rollout plan |
