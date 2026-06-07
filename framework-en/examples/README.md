# DITA Manufacturing Framework — Examples

This directory contains bilingual example files demonstrating the DITA Manufacturing Framework in practice. The examples use a fictional company (Example Manufacturing / 示例制造) and their CNC machining center product lines.

## Structure

```
examples/
├── governance/              # Filled governance documents
│   ├── CHANGE-AUTHORITY-FILLED.md
│   └── CONTENT-OWNERSHIP-FILLED.md
├── maps/                    # DITA maps for web navigation
│   └── PRODUCT-DITAMAP.ditamap
├── metadata/                # Metadata sample
│   └── SAMPLE-METADATA.xml
└── topics/                  # DITA topic examples
    ├── MC5000-SAFETY-CONCEPT.dita        (Concept)
    ├── SPINDLE-ALIGNMENT-TASK.dita       (Task)
    ├── MC5000-SPECS-REFERENCE.dita       (Reference)
    ├── ALARM-700016-TROUBLESHOOTING.dita (Troubleshooting)
    ├── GLOSS-LOTO.dita                   (Glossary Entry)
    └── HAZARD-FLYING-CHIPS.dita          (Hazard Statement)
```

Chinese examples are available in `../../framework-zh/examples/`. Language-specific examples follow the structure above.

## Usage

All examples follow the templates in `../templates/` and the architecture defined in this directory (English) / `../../framework-zh/` (Chinese).

- **English topics** serve as the source language (`xml:lang="en-US"`)
- **Chinese topics** use `xml:lang="zh-CN"` and reference GB/T standards (Chinese national standards equivalent to ISO)

## Product Context

- MC-5000 VMC — Vertical Machining Center
- MC-8000 5-Axis — 5-Axis Machining Center
- DR-2000 High-Speed — High-Speed Engraving Machine
