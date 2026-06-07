# Content Ownership Matrix — 示例制造 (Filled Example)

> Reference: Framework → 01-GOVERNANCE → 02-CONTENT-OWNERSHIP-MATRIX.md
>
> This is a fully populated example for Example Manufacturing Co., Ltd.

## 1. Product Documentation Ownership

| Product Line | Owner | Deputy | Team | Review Cycle |
|---|---|---|---|---|
| **MC-5000 VMC** | Chen Wei (Safety Eng.) | Li Na (Tech Writing) | 4 writers + 1 SME | Quarterly |
| **MC-8000 5-Axis** | Zhang Li (Applications) | Wang Jie (Tech Writing) | 3 writers + 2 SMEs | Quarterly |
| **DR-2000 High-Speed** | Liu Yang (Product Eng.) | Zhao Min (Tech Writing) | 2 writers + 1 SME | Bi-annual |

## 2. Content Domain Ownership

| Domain | Owner Department | Key Contact | Subject Matter Experts |
|---|---|---|---|
| **Safety & Compliance** | Safety Engineering | Chen Wei | ISO 12100 / 13849 / 14120 |
| **Electrical Systems** | Electrical Engineering | Huang Tao | IEC 60204-1, NFPA 79 |
| **Mechanical Systems** | Mechanical Engineering | Sun Lei | Spindle, axis, cooling |
| **Software / CNC** | Controls Engineering | Zhou Kai | SINUMERIK ONE, HMI |
| **Installation & Commissioning** | Field Service | Ma Bin | On-site setup, calibration |
| **Maintenance & Repair** | After-Sales Service | Xu Feng | Lube schedules, spare parts |
| **Training Materials** | Training Department | Gao Ling | Operator, advanced, e-learning |

## 3. Content-Type Ownership Matrix

| Content Type | Owner | Reviewer | Approver | SLA |
|---|---|---|---|---|
| **Concept** (architectural) | Engineering Director | Technical Lead | CTO | 5 business days |
| **Task** (procedures) | Technical Writing Lead | SME (relevant domain) | Dept. Manager | 3 business days |
| **Reference** (specifications) | Product Manager | Engineering Lead | Engineering Director | 5 business days |
| **Troubleshooting** | Field Service Lead | Technical Support Lead | Service Director | 3 business days |
| **Training** | Training Lead | Technical Writing Lead | HR Director | 7 business days |

## 4. Language Ownership

| Language | Owner (Internal/External) | TMS Vendor | Target SLA |
|---|---|---|---|
| **English (source)** | ExampleCorp Doc Team | N/A (source) | N/A |
| **Chinese (Simplified)** | ExampleCorp Doc Team | N/A (source tier-1) | N/A |
| **German** | EuroLingua GmbH | SDL Trados | 5 business days |
| **French** | EuroLingua GmbH | SDL Trados | 5 business days |
| **Spanish** | TransGlobal Solutions | memoQ | 5 business days |
| **Japanese** | TransGlobal Solutions | memoQ | 7 business days |
| **Korean** | TransGlobal Solutions | memoQ | 7 business days |

## 5. Review & Approval Chain (Example: MC-5000 Safety Concept)

```
Author (Chen Wei, Safety Eng.)
  └─→ Technical Review (Li Na, Tech Writing Lead) — 1 business day
        └─→ SME Review (Huang Tao, Electrical Eng.) — 2 business days
              └─→ Compliance Review (External ISO Auditor) — 3 business days
                    └─→ Editorial Review (Zhang Wei, Editor) — 1 business day
                          └─→ Approval (Wang Ming, Eng. Director) — 1 business day
                                └─→ PUBLISHED
```

**Total cycle time: 8 business days** (within the framework's < 8 day SLA)
