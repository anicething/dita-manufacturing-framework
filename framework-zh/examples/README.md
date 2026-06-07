# DITA Manufacturing Framework — Examples

This directory contains bilingual example files demonstrating the DITA Manufacturing Framework in practice. The examples use a fictional company (Example Manufacturing / 示例制造) and their CNC machining center product lines.

## Structure

```
examples/
├── maps/                    # 中文 DITA 图
│   └── PRODUCT-DITAMAP.ditamap
└── topics/                  # 中文 DITA 主题示例
    ├── MC5000-SAFETY-CONCEPT.dita        (概念)
    ├── SPINDLE-ALIGNMENT-TASK.dita       (任务)
    ├── MC5000-SPECS-REFERENCE.dita       (参考)
    ├── ALARM-700016-TROUBLESHOOTING.dita (故障排除)
    ├── GLOSS-LOTO.dita                   (术语)
    └── HAZARD-FLYING-CHIPS.dita          (危险声明)
```

英文示例位于 `../../framework-en/examples/`。

## Usage

All examples follow the templates in `../templates/` and the architecture defined in `../../framework-en/` (English) / this directory (Chinese).

- **English topics** serve as the source language (`xml:lang="en-US"`)
- **Chinese topics** use `xml:lang="zh-CN"` and reference GB/T standards (Chinese national standards equivalent to ISO)

## Product Context

- MC-5000 VMC — Vertical Machining Center
- MC-8000 5-Axis — 5-Axis Machining Center
- DR-2000 High-Speed — High-Speed Engraving Machine
