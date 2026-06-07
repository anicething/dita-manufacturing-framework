---
marp: true
theme: dita-training
class: invert
paginate: true
---

<!-- _class: title -->

# Content Metrics & KPIs
## Measuring and Improving Documentation Performance

**DITA Expert Level - Module 3**
Example Manufacturing Co., Ltd.
v1.1 | 2026-06-07

---

<!-- _class: divider -->

# Agenda

1. Why Measure? The Business Case for DITA KPIs
2. Reuse Ratio: The Foundation Metric
3. Content Coverage Rate
4. Readability Scoring for Technical Documentation
5. Compliance Scoring (ISO Standards Adherence)
6. Localization Efficiency Metrics
7. Quality Metrics: Defect Density and Rework Rate
8. Publishing Velocity
9. Information Architecture Metrics
10. Dashboard Design and Visualization
11. Benchmark Targets for Manufacturing Documentation
12. KPI-Driven Improvement Planning
13. Exercise: MC-5000 Reuse Ratio Calculation
14. Summary and Key Takeaways

---

# Learning Objectives

By the end of this module, you will be able to:

- Define and calculate the **10 core KPIs** for DITA documentation
- Build a **reuse ratio model** using conref counts and topic counts
- Design a **content coverage matrix** against a product feature list
- Apply **readability formulas** suitable for technical documentation
- Establish **benchmark targets** for manufacturing documentation teams
- Create a **KPI dashboard** using collected metrics
- Develop a **data-driven improvement plan** for a documentation team

---

<!-- _class: divider -->

# Part 1: Why Measure?

---

# The Business Case for DITA KPIs

Documentation is a **strategic asset**, not a cost center. Measurement transforms it from an art into a science.

```
Without KPIs:                      With KPIs:
"We work hard"            ->       "Our reuse ratio improved from 38% to 64%"
"Quality is good"         ->       "Defect density: 0.12 per topic"
"Translation is expensive"->       "Localization cost per word: $0.18"
```

> "What gets measured gets managed." -- Peter Drucker

---

# The DITA KPI Hierarchy

```
                    +-----------------------+
                    |   Business Impact      |
                    |  (ROI, Cost Savings)   |
                    +-----------+-----------+
                                |
                    +-----------+-----------+
                    |   Efficiency Metrics   |
                    | (Reuse, Velocity, TAT) |
                    +-----------+-----------+
                                |
        +-----------------------+-----------------------+
        |                       |                       |
+-------+-------+       +-------+-------+       +-------+-------+
|    Quality    |       |   Coverage    |       |  Compliance   |
|   (Defects,   |       | (Completeness,|       | (Standards,   |
|  Readability) |       |   Accuracy)   |       |    Audit)     |
+---------------+       +---------------+       +---------------+
```

---

# Data Sources for DITA Metrics

| Data Source | What It Provides | Tool/Approach |
|-------------|-----------------|---------------|
| **DITA Map** | Topic counts, hierarchy depth | XPath, custom script |
| **Content files** | Conref count, word count | Saxon, Python, XSLT |
| **CCMS/CMS** | Version history, author stats | CCMS API (Astoria, RWS, etc.) |
| **DITA-OT logs** | Build time, warnings/errors | Jenkins/GitHub Actions logs |
| **Translation memory** | Match rates, fuzzy matches | TMS dashboard (WorldServer, memoQ) |
| **Support tickets** | Defect reports, user feedback | Jira, ServiceNow |
| **Google Analytics** | Page views, search queries, bounces | Analytics dashboard |

---

<!-- _class: divider -->

# Part 2: Core DITA KPIs

---

# KPI 1: Reuse Ratio

The single most important metric for measuring DITA ROI.

```
Reuse Ratio = Number of reused elements (conrefs, keyrefs)
              -------------------------------------------
              Total number of content elements (topics + inline elements)
```

**Simple formulation (topic-level):**

```
Reuse Ratio = Topic instances in maps
              ---------------------
              Unique topic count (in repository)
```

A reuse ratio of **3.0** means each unique topic is used on average in 3 different maps.

---

# Reuse Ratio: Calculation Walkthrough

**Scenario: MC-5000 CNC Machining Center documentation set**

```
Unique topics in repository:   247
Topic instances across all maps:  618

Reuse Ratio = 618 / 247 = 2.50
```

**What 2.50 means:** Each unique topic appears in an average of 2.5 publications.

```
Map                          Topics Used  Unique  Reuse Factor
--------------------------------------------------------------
MC-5000 Operator Manual           180      247       0.73
MC-5000 Maintenance Manual        215      247       0.87
MC-5000 Parts Catalog             145      247       0.59
MC-5000 Installation Guide         78      247       0.32
--------------------------------------------------------------
Total                             618      247       2.50
```

---

# Reuse Ratio: Conref-Level Granularity

For a more precise measurement, count individual element reuse.

```xml
<!-- safety/warning-hazard.mod -->
<ph id="high_pressure_warning" conref="hazard.dita#haz/high_pressure">
  High-pressure coolant hazard -- wear eye protection.
</ph>

<!-- This single conref appears in 47 topics -->
```

```xml
<!-- Count conref references across all topics -->
<!-- XSLT approach -->
<xsl:template match="*[@conref]">
  <xsl:value-of select="1"/>
</xsl:template>
```

```bash
# Command-line count
grep -r '@conref' src/ | wc -l
#> 1647 conref references found

# Element-level reuse ratio
echo "1647 / 247 = 6.67"  # conrefs per unique topic
```

---

# Reuse Ratio: Benchmark Table

| Industry Segment | Beginner (Year 1) | Developing (Year 2) | Mature (Year 3+) | Expert (Year 5+) |
|------------------|:---:|:---:|:---:|:---:|
| **General Manufacturing** | 1.2-1.5 | 1.8-2.5 | 3.0-4.0 | 5.0+ |
| **CNC/Machinery** | 1.5-2.0 | 2.5-3.5 | 4.0-6.0 | 7.0+ |
| **Automotive** | 2.0-3.0 | 4.0-6.0 | 8.0-12.0 | 15.0+ |
| **Aerospace/Defense** | 1.5-2.0 | 3.0-4.0 | 5.0-8.0 | 10.0+ |
| **Medical Devices** | 1.0-1.5 | 2.0-3.0 | 4.0-6.0 | 8.0+ |

**ExampleCorp target:** Move from 2.5 (current) to 6.0 within 24 months.

---

# KPI 2: Content Coverage Rate

Measures what percentage of a product's features have corresponding documentation.

```
Coverage Rate = Number of documented features
                ----------------------------- x 100
                Total number of product features
```

| Feature Area | Features | Documented | Coverage % |
|-------------|:---:|:---:|:---:|
| Spindle System | 12 | 11 | 91.7% |
| Coolant System | 8 | 8 | 100% |
| Tool Changer (ATC) | 10 | 7 | 70.0% |
| CNC Controller (Fanuc) | 25 | 24 | 96.0% |
| Servo Drive System | 6 | 4 | 66.7% |
| Chip Conveyor | 5 | 5 | 100% |
| **Total MC-5000** | **66** | **59** | **89.4%** |

---

# Building a Feature-Coverage Matrix

Use a DITA subject scheme map to enforce coverage.

```xml
<!-- coverage-subjectScheme.ditamap -->
<subjectScheme>
  <subjectdef keys="dr5000_features">
    <subjectdef keys="spindle_system">
      <subjectdef keys="spindle_assy"/>
      <subjectdef keys="spindle_motor"/>
      <subjectdef keys="spindle_bearings"/>
      <subjectdef keys="spindle_cooling"/>
      <!-- ... 8 more features -->
    </subjectdef>
    <subjectdef keys="atc_system">
      <subjectdef keys="tool_magazine"/>
      <subjectdef keys="tool_changer_arm"/>
      <subjectdef keys="tool_presetter"/>
      <!-- ... 7 more features -->
    </subjectdef>
  </subjectdef>
</subjectScheme>
```

---

# KPI 3: Readability Score

Technical documentation should balance precision with accessibility.

| Metric | Formula | Target Range | Tools |
|--------|---------|:---:|------|
| **Flesch-Kincaid** | 206.835 - 1.015(words/sentences) - 84.6(syllables/words) | 40-60 (fairly difficult) | Acrolinx, Grammarly |
| **Gunning Fog** | 0.4 x (words/sentences + 100 x (complex_words/words)) | 10-14 (technical) | Online calculators |
| **SMOG** | 1.043 x sqrt(30 x polysyllables/sentences) + 3.13 | 8-12 | Readability formulas |
| **DITA-DQI** | Custom: combines consistency, brevity, terminology | 80-100 | Custom XSLT |

**Target for MC-5000 manuals:**
- Operator Guide: Flesch-Kincaid **50-55** (accessible to machine operators)
- Maintenance Manual: Flesch-Kincaid **40-48** (for trained technicians)
- Service Bulletins: Flesch-Kincaid **35-42** (for field engineers)

---

# Readability Automation for DITA

```xml
<!-- Check readability per topic using a custom DITA-OT plugin -->
<!-- readability-check.xsl -->

<xsl:template match="*[contains(@class, ' topic/topic ')]">
  <xsl:variable name="words" select="
    count(tokenize(normalize-space(string(.)), '\s+'))
  "/>
  <xsl:variable name="sentences" select="
    count(tokenize(normalize-space(string(.)), '[.?!]\s+'))
  "/>
  <xsl:variable name="fk_score" select="
    if ($sentences > 0) then
      206.835 - 1.015 * ($words div $sentences) - 
      84.6 * (count(tokenize(string(.), '[aeiou]+')) div $words)
    else 0
  "/>
  <xsl:if test="$fk_score > 60">
    <xsl:message terminate="no">
      WARNING: Topic <xsl:value-of select="@id"/> Flesch-Kincaid score 
      <xsl:value-of select="format-number($fk_score, '0.0')"/> is too high.
      Consider simplifying for target audience.
    </xsl:message>
  </xsl:if>
</xsl:template>
```

---

# KPI 4: Compliance Scoring

Manufacturing documentation must adhere to ISO standards. Measure it.

| ISO Standard | Requirement | Measurement Method |
|-------------|-------------|-------------------|
| **ISO 12100** | Safety of machinery -- risk assessment | % of hazard topics with required warnings |
| **ISO 20607** | Machine instruction handbook structure | Checklist scoring of sections present |
| **ISO 82079-1** | Preparation of instructions for use | 22-point assessment rubric |
| **ANSI Z535.6** | Product safety information | Signal word usage audit |
| **CE Marking** | EU machinery directive 2006/42/EC | Declaration of conformity cross-check |

```
Compliance Score = Sum(passed checks per standard) / Sum(total checks) x 100
```

---

# Compliance Scoring: Schema Implementation

```xml
<!-- compliance-checklist.dita -->
<reference id="dr5000_compliance" xml:lang="en">
  <title>MC-5000 Compliance Checklist</title>
  <refbody>
    <section>
      <title>ISO 20607 Compliance Items</title>
      <table>
        <tgroup cols="3">
          <thead>
            <row>
              <entry>Section</entry>
              <entry>Required Content</entry>
              <entry>Status</entry>
            </row>
          </thead>
          <tbody>
            <row>
              <entry>4.1</entry>
              <entry>Intended use description</entry>
              <entry>PASS (dr5000_intro.dita)</entry>
            </row>
            <row>
              <entry>4.2</entry>
              <entry>Reasonably foreseeable misuse</entry>
              <entry>PASS (dr5000_safety.dita#misuse)</entry>
            </row>
            <row>
              <entry>4.3</entry>
              <entry>Residual risk disclosure</entry>
              <entry>PASS (dr5000_residual_risks.dita)</entry>
            </row>
            <row>
              <entry>5.1</entry>
              <entry>Transport and handling instructions</entry>
              <entry>MISSING -- add topic</entry>
            </row>
          </tbody>
        </tgroup>
      </table>
    </section>
  </refbody>
</reference>
```

---

# KPI 5: Localization Efficiency

Measure the cost and speed of translating DITA content.

```
Localization Cost per Word = Total translation spend
                             ----------------------
                             Total source words translated
                             
Translation Memory (TM) Leverage = (Fuzzy + Exact matches) / Total segments
```

| Metric | Calculation | MC-5000 Current | Target |
|--------|------------|:---:|:---:|
| **Source word count** | XML word count (excl. markup) | 124,500 words | -- |
| **TM Leverage Rate** | (Exact + ICE + Fuzzy) / Total | 62% | 80% |
| **Cost per Word** | Total localization spend / words | $0.24 | $0.15 |
| **Turnaround Days** | Time from handoff to delivery | 12 days (per language) | 7 days |
| **Languages Supported** | Active locale count | 4 (en, zh-CN, de, ja) | 8 |

---

# DITA Impact on Localization

DITA reuse directly drives translation savings.

| Reuse Ratio | TM Leverage | Est. Cost per Word | Annual Savings (vs. 1.0) |
|:---:|:---:|:---:|---:|
| 1.0 | 0% | $0.30 | $0 (baseline) |
| 1.5 | 18% | $0.25 | $31,125 |
| 2.5 | 35% | $0.20 | $62,250 |
| 4.0 | 55% | $0.14 | $99,600 |
| 6.0 | 72% | $0.10 | $124,500 |

**Assumptions:** 124,500 source words, 4 target languages, $0.30/word baseline.

---

# KPI 6: Defect Density

Track content defects to measure quality improvement.

```
Defect Density = Number of reported content defects
                 ---------------------------------
                      Total number of topics
```

| Defect Type | Count (Q1 2026) | % of Total |
|-------------|:---:|:---:|
| Technical Inaccuracy | 12 | 26% |
| Broken Link/Conref | 15 | 32% |
| Typo/Grammar | 9 | 19% |
| Missing Content | 6 | 13% |
| Formatting/Rendering | 5 | 11% |
| **Total** | **47** | **100%** |
| **Defect Density** | **47/247 = 0.19/topic** | |

**Target:** Reduce to 0.08/topic within 12 months.

---

# KPI 7: Publishing Velocity

How fast can you get content from draft to published?

```
Cycle Time = Days from first draft to publication
Velocity = Number of topics published / Time period
```

| Phase | Average Duration | Optimization Target |
|-------|:---:|:---:|
| Draft Authoring | 3.2 days/topic | 2.0 days |
| Peer Review | 2.1 days/topic | 1.5 days |
| SME Review | 4.8 days/topic | 3.0 days |
| Editing | 1.5 days/topic | 1.0 day |
| Approval | 2.3 days/topic | 1.5 days |
| **Total Cycle** | **13.9 days/topic** | **9.0 days** |

---

# KPI 8: Information Architecture Health

Measure the structural quality of your DITA content.

| Metric | Description | Healthy Range | MC-5000 Score |
|--------|-------------|:---:|:---:|
| **Topic Granularity** | Average words/topic | 200-800 | 504 |
| **Map Depth** | Maximum nesting level | 2-5 levels | 3 |
| **Related Links Ratio** | Links per topic | 1-4 | 1.8 |
| **Orphan Topics** | Topics not in any map | 0% of total | 3.2% (8 topics) |
| **Metadata Completeness** | % of topics with audience/platform/product | > 95% | 87% |
| **Key Definition Coverage** | Defined keys / referenced keys | 100% | 94% |
| **Conditional Attribute Usage** | % of element-level conditional markup | < 30% | 12% |

---

<!-- _class: divider -->

# Part 3: Dashboard Design

---

# KPI Dashboard Architecture

```
+------------------------------------------------------------------+
|                    KPI Dashboard Architecture                      |
|                                                                   |
|  +----------+   +----------+   +----------+   +----------+      |
|  |  CCMS    |   |  DITA-OT |   |  GitHub  |   |  Support |      |
|  |  (RWS)   |   |  Build   |   |  Actions |   |  Tickets |      |
|  |          |   |  Logs    |   |  Logs    |   |  (Jira)  |      |
|  +----+-----+   +----+-----+   +----+-----+   +----+-----+      |
|       |              |              |              |              |
|       +--------------+--------------+--------------+              |
|                      |              |                             |
|              +-------+--------------+-------+                     |
|              |     ETL Script (Python)       |                     |
|              |   Extract, Transform, Load    |                     |
|              +---------------+--------------+                     |
|                              |                                    |
|                     +--------+--------+                          |
|                     |  Data Store      |                          |
|                     |  (PostgreSQL /   |                          |
|                     |   SQLite)        |                          |
|                     +--------+--------+                          |
|                              |                                    |
|              +---------------+--------------+                   |
|              |      Dashboard Platform        |                   |
|              |   (Grafana / PowerBI /         |                   |
|              |    Elasticsearch+Kibana)       |                   |
|              +-------------------------------+                   |
+------------------------------------------------------------------+
```

---

# KPI Data Collection Script

```python
#!/usr/bin/env python3
"""
dita_metrics_collector.py
Collects DITA KPIs from the DITA Manufacturing Framework.
"""

import os
import re
import xml.etree.ElementTree as ET
from pathlib import Path
from collections import Counter

SRC_DIR = Path("src")

def count_topics(source_dir: Path) -> int:
    """Count unique DITA topics in repository."""
    return len(list(source_dir.rglob("*.dita")))

def count_conrefs(source_dir: Path) -> int:
    """Count all conref references in DITA files."""
    count = 0
    for dita_file in source_dir.rglob("*.dita"):
        content = dita_file.read_text(encoding="utf-8")
        count += len(re.findall(r'@conref', content))
    return count

def calculate_reuse_ratio(unique_topics: int, map_instances: int) -> float:
    """Calculate topic-level reuse ratio."""
    return round(map_instances / unique_topics, 2) if unique_topics > 0 else 0.0

def estimate_word_count(source_dir: Path) -> int:
    """Estimate word count excluding XML markup."""
    total = 0
    for dita_file in source_dir.rglob("*.dita"):
        text = re.sub(r'<[^>]+>', ' ', dita_file.read_text(encoding="utf-8"))
        total += len(text.split())
    return total

if __name__ == "__main__":
    unique = count_topics(SRC_DIR)
    conrefs = count_conrefs(SRC_DIR)
    print(f"=== DITA Metrics Report ===")
    print(f"Unique topics:       {unique}")
    print(f"Conref references:   {conrefs}")
    print(f"Conref/topic ratio:  {round(conrefs / unique, 2)}")
    print(f"Estimated word count: {estimate_word_count(SRC_DIR):,}")
```

---

# Dashboard Mockup: Executive View

```
+-------------------------------------------------------------------+
|  ExampleCorp Documentation KPI Dashboard           Updated: 2026-06-07 |
+-------------+-------------+-------------+-------------+------------+
|   Reuse     |  Coverage   | Readability | Compliance  |  Loc. Cost |
|   Ratio     |    Rate     |  Score (FK) |    Score    |  Per Word  |
|   +---+     |   +----+    |   +----+    |   +----+    |  +----+    |
|   |2.5|     |   |89% |    |   | 48 |    |   |92% |    |  |$0.2|    |
|   +---+     |   +----+    |   +----+    |   +----+    |  +----+    |
|  Target:6.0 | Target:95%  | Target:45-55| Target:98%  | Target:$0.15|
|   Up 0.3    |   Up 1.2%   |   Down -2.1 |   Up 3.5%   |   Down -0.03|
+-------------+-------------+-------------+-------------+------------+
|                                                                     |
|  Reuse Ratio Trend (6 months)       Defect by Category              |
|  3.0|                        *      Broken Links    ==========       |
|     |                    * *         Inaccuracy      =======         |
|  2.5|  *---*---*---*                Typo/Grammar    =====           |
|     |                               Missing Content ===             |
|  2.0|                               Formatting      ==              |
|     +--Jan--Feb--Mar--Apr--May--Jun                                 |
+---------------------------------------------------------------------+
```

---

# Setting SMART KPI Targets

| KPI | Specific | Measurable | Achievable | Relevant | Time-Bound |
|-----|----------|------------|------------|----------|------------|
| Reuse Ratio | Topic-level reuse | Conref+map count | 2.5->4.0 first year | Drives cost savings | By 2026-12-31 |
| Coverage | Feature-to-topic | Subject scheme matrix | 89%->95% | Customer satisfaction | By Q3 2026 |
| Defect Density | Bugs per topic | Jira + topic count | 0.19->0.10 | Reduces support load | By Q4 2026 |
| Loc Cost | Per-word average | TMS integration | $0.24->$0.18 | Budget efficiency | By 2026-12-31 |

---

<!-- _class: divider -->

# Part 4: Manufacturing Benchmarks

---

# Industry Benchmarks for Manufacturing Documentation

| KPI | Below Average | Industry Average | Best in Class | ExampleCorp Current |
|-----|:---:|:---:|:---:|:---:|
| Reuse Ratio | < 1.5 | 2.5-4.0 | 8.0+ | 2.5 |
| Coverage Rate | < 70% | 85-92% | 98%+ | 89% |
| Flesch-Kincaid | < 30 | 40-55 | 45-55 (targeted) | 48 |
| Defect Density | > 0.5 | 0.15-0.30 | < 0.08 | 0.19 |
| Cycle Time (days/topic) | > 20 | 10-15 | < 7 | 13.9 |
| TM Leverage | < 30% | 50-65% | 85%+ | 62% |
| Orphan Rate | > 8% | 2-5% | < 1% | 3.2% |
| Metadata Completeness | < 70% | 85-95% | 99%+ | 87% |

---

# KPI-Driven Improvement Planning

The **PDCA Cycle** applied to DITA documentation:

```
+-------------------------------------+
| PLAN: Identify KPI gaps and root    |
|       cause analysis                |
|   "Our coverage of ATC features     |
|    is only 70%. Why?"               |
+-------------+-----------------------+
              |
+-------------+-----------------------+
| DO: Execute improvement actions     |
|   "Assign 2 writers to document     |
|    10 missing ATC procedures"       |
+-------------+-----------------------+
              |
+-------------+-----------------------+
| CHECK: Re-measure KPIs after        |
|        changes. Did coverage        |
|        improve?                      |
+-------------+-----------------------+
              |
+-------------+-----------------------+
| ACT: Standardize what worked.       |
|      Identify next gap. Repeat.     |
|   "Add ATC documentation to the     |
|    feature-coverage subject scheme" |
+-------------------------------------+
```

---

# Root Cause Analysis for Underperforming KPIs

| Symptom | Possible Root Causes | Investigation |
|---------|---------------------|---------------|
| Low Reuse Ratio | Writers unaware of existing content; poor search; siloed teams | Content audit; writer interviews |
| Low Coverage | No product-doc alignment; SME unavailable; unclear ownership | Feature->topic gap analysis |
| High Defect Density | SME review skipped; no style guide adherence; rushed publishing | Defect trend analysis by author |
| Long Cycle Time | Bottleneck at SME review; serial approval chain; manual steps | Value stream mapping |
| Low TM Leverage | Too much original content; lack of ICE matching; inconsistent terminology | TM analysis report |

---

<!-- _class: divider -->

# Part 5: Hands-On Exercise

---

<!-- _class: exercise -->

# Exercise: Calculate Reuse Ratio for MC-5000

**Scenario:** You are the documentation lead for the MC-5000 CNC Machining Center. The DITA content repository contains the following statistics.

**Given data:**

| Category | Count |
|----------|------:|
| Unique DITA topics in repository | 312 |
| Topics referenced in Operator Manual map | 205 |
| Topics referenced in Maintenance Manual map | 238 |
| Topics referenced in Parts Catalog map | 167 |
| Topics referenced in Installation Guide map | 89 |
| Total conref elements across all files | 2,184 |

**Calculate:**

1. Topic-level Reuse Ratio (total map instances / unique topics)
2. Conref density (conrefs per unique topic)
3. Reuse Ratio for each individual publication
4. Which publication has the highest reuse factor and why?

---

<!-- _class: exercise -->

# Exercise: Solution

**1. Topic-level Reuse Ratio:**

```
Total map instances = 205 + 238 + 167 + 89 = 699
Reuse Ratio = 699 / 312 = 2.24
```

**2. Conref density:**

```
2184 / 312 = 7.0 conrefs per unique topic
```

**3. Per-publication reuse:**

| Publication | Topics | Ratio | Comment |
|-------------|--------|:---:|---------|
| Operator Manual | 205/312 | 0.66 | 66% of unique topics used |
| Maintenance Manual | 238/312 | 0.76 | **Highest** -- broadest coverage |
| Parts Catalog | 167/312 | 0.54 | Focused on replaceable components |
| Installation Guide | 89/312 | 0.29 | Narrowest scope (one-time setup) |

**4. Maintenance Manual has highest reuse factor because maintenance procedures span the widest range of subsystems.**

---

<!-- _class: exercise -->

# Exercise: Build a 90-Day Metric Improvement Plan

| Day | Action | KPI Target | Owner |
|-----|--------|------------|-------|
| 1-15 | Run content audit; identify top-20 reuse candidates | Establish baseline | Content Architect |
| 16-30 | Add audience, platform, product metadata to all orphan topics | Metadata 87% -> 95% | Tech Writer Team |
| 31-45 | Restructure 10 most-repeated procedures as reusable components | Reuse 2.24 -> 2.8 | DITA Specialist |
| 46-60 | Integrate Acrolinx readability checking into authoring workflow | FK Score in 45-55 range | Tools Team |
| 61-75 | Build compliance subject scheme for ISO 20607 checklist | Compliance 92% -> 98% | Content Architect |
| 76-90 | Launch Grafana dashboard with weekly refresh | All KPIs visible | DevOps + Doc Lead |

**Expected 90-day outcomes:**
- Reuse ratio: +25% (2.24 -> 2.8)
- Metadata completeness: +8% (87% -> 95%)
- Compliance score: +6% (92% -> 98%)

---

<!-- _class: divider -->

# Summary

---

# Key Takeaways

| No. | Takeaway |
|-----|----------|
| 1 | **Reuse Ratio** is the #1 ROI metric: measure at both topic and element levels |
| 2 | **Content Coverage** must be tied to a product feature matrix -- use subject scheme maps |
| 3 | **Readability** scores should be audience-specific: operators need different levels from engineers |
| 4 | **Compliance scoring** maps ISO/ANSI requirements to DITA topics -- automate the checklist |
| 5 | **Localization savings** are the easiest ROI to calculate: TM leverage x reuse = cost reduction |
| 6 | **Defect density** tracking reveals quality trends -- integrate with support ticket systems |
| 7 | **Dashboards** must serve two audiences: executives (summary) and writers (detail) |
| 8 | **PDCA cycles** ensure continuous improvement: Plan -> Do -> Check -> Act, quarterly |
| 9 | **Benchmarks** set targets: know where you stand vs. industry best-in-class |
| 10 | **Data collection** must be automated: manual KPI gathering does not scale |

---

# DITA KPI Quick Reference Card

```
+------------------------------------------------------+
|              DITA KPI QUICK REFERENCE                 |
+------------------------------------------------------+
| KPI              | Formula          | Good | Great   |
+------------------+------------------+------+---------+
| Reuse Ratio      | Instances/Unique | 3.0  | 6.0+    |
| Coverage Rate    | Doc'd/Features   | 90%  | 98%+    |
| FK Readability   | FK Formula       | 40-55| 45-50   |
| Compliance Score | Passed/Total     | 95%  | 99%+    |
| TM Leverage      | (Exact+Fuzzy)/Tot| 60%  | 85%+    |
| Defect Density   | Defects/Topics   |<0.15 | <0.08   |
| Cycle Time       | Days from draft  |<12   | <7      |
| Metadata Cover   | % w/ metadata    | 95%  | 99%+    |
| Orphan Rate      | Unmapped/total   |<5%   | <1%     |
| Loc Cost/Word    | $Spend/Words     |$0.18 | $0.10   |
+------------------------------------------------------+
```

---

# Recommended Tools

| Purpose | Tool | Cost |
|---------|------|------|
| Terminology Management | Acrolinx, TermWeb | Paid |
| Readability Scoring | Grammarly Business, Acrolinx | Paid |
| Dashboarding | Grafana (open-source), PowerBI | Free/Paid |
| Data Collection | Python + lxml, Saxon XSLT | Free |
| Translation Management | memoQ, RWS WorldServer | Paid |
| Defect Tracking | Jira + custom fields | Paid |
| Content Audit | Oxygen XML, custom XSLT | Paid/Free |
| Link Checking | DITA-OT link checker, LinkTester | Free/Paid |

---

# Next Module

**Module 4: Team Management & DITA Center of Excellence**

- Building a CoE from scratch
- Roles, responsibilities, and career paths
- Competency matrices and onboarding programs
- Cross-functional team management
- Budgeting for DITA infrastructure

---

<!-- _class: note -->

**Module Version:** v1.1 | **Last Updated:** 2026-06-07
**Author:** ExampleCorp Documentation CoE
**Prerequisites:** DITA Expert Modules 1-2 (or equivalent knowledge)
**Estimated Duration:** 3.5 hours (presentation + exercises)
