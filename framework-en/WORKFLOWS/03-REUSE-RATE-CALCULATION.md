# Content Reuse Rate Calculation & Optimization

**Document ID:** WF-003
**Version:** 1.0
**Last Updated:** 2026-06-07
**Status:** Approved

---

## Table of Contents

1. [Why Measure Reuse?](#1-why-measure-reuse)
2. [Reuse Rate Definition & Formula](#2-reuse-rate-definition--formula)
3. [Measuring Reuse by Type](#3-measuring-reuse-by-type)
4. [Automated Reuse Calculation Script](#4-automated-reuse-calculation-script)
5. [CI/CD Reuse Gate Integration](#5-cicd-reuse-gate-integration)
6. [Reuse Opportunity Identification](#6-reuse-opportunity-identification)
7. [Reuse Optimization Workflow](#7-reuse-optimization-workflow)
8. [Reporting & Dashboard](#8-reporting--dashboard)
9. [Targets & Benchmarks](#9-targets--benchmarks)

---

## 1. Why Measure Reuse?

DITA's value proposition depends on content reuse. Without measurement, teams cannot:

- Justify the DITA investment to leadership
- Identify under-performing content areas
- Track improvement over time
- Set meaningful targets for authors
- Detect regressions when content is duplicated

**Industry Benchmarks (Munne 2024, ACM SIGDOC 2024):**

| Maturity Level | Reuse Rate | Typical Timeline |
|---------------|-----------|-----------------|
| Initial adoption | 5-15% | Months 1-6 |
| Established practice | 15-30% | Months 7-18 |
| Optimized | 30-40% | Months 19-36 |
| Industry-leading | 40%+ | 36+ months |

**Target for this framework: 35-40% by Month 12**

---

## 2. Reuse Rate Definition & Formula

### 2.1 Core Formula

```
Reuse Rate = (Reused Topics + Reference Instances) / (Total Topics Produced) × 100%
```

Where:

| Term | Definition |
|------|-----------|
| **Reused Topics** | Topics referenced by 2+ maps or included via `conref` / `conkeyref` |
| **Reference Instances** | Each occurrence of a reused topic across all maps |
| **Total Topics Produced** | Unique topics in the repository (counted once) |

### 2.2 Weighted Reuse Rate (Recommended)

Simple topic counts favor small reference topics. A weighted formula accounts for the actual content volume being reused:

```
Weighted Reuse Rate = Σ(Reused Topic Word Count) / Σ(All Topic Word Count) × 100%
```

This gives a more accurate picture of how much content authors are NOT rewriting.

### 2.3 Granular Reuse Types

| Reuse Mechanism | Measurement Method | Weight Factor |
|----------------|-------------------|---------------|
| **Conref** (`<conref>`) | Count `conref` attributes in topic body | 1.0 (inline) |
| **Conkeyref** (`<conkeyref>`) | Count `conkeyref` attributes | 1.0 (inline) |
| **Keyref** (`<keyword keyref>`) | Count `keyref` attribute usage | 0.3 (single value) |
| **Topic-level map reuse** | Topics appearing in 2+ ditamaps | Equal to topic word count |
| **Ditamap reuse** | Ditamaps referenced by 2+ bookmaps | 1.0 per inclusion |

### 2.4 Practical Example

Repository snapshot:

```
Product A Manual:     150 topics (50 reused from shared)
Product B Manual:     120 topics (40 reused from shared)
Shared Library:        80 topics (all shared)
```
```
Total unique topics:      150 + 120 + 80 - 40(shared) = 310
Reused topic instances:    50 + 40 = 90
Reuse Rate:                90 / 310 = 29.0%
```

---

## 3. Measuring Reuse by Type

### 3.1 Conref Analysis

Conref reuse occurs when a topic references a fragment from another topic:

```xml
<p conref="shared/common-precautions.dita#precautions/warning-electrical"/>
```

**To measure:** Count all `<* conref="..."` attributes in the topic body.

### 3.2 Keyref Analysis

Keyref reuse occurs via variable keys:

```xml
<p>Always wear <keyword keyref="ppe-safety-glasses"/> when operating.</p>
```

**To measure:** Count all `<keyword keyref="..."` and `<ph keyref="..."` elements.

### 3.3 Map-Level Reuse

Map-level reuse occurs when a topic is referenced from multiple ditamaps:

```
product-a-manual.ditamap         product-b-manual.ditamap
  ├── safety-intro.dita            ├── safety-intro.dita        ← REUSED
  ├── product-a-setup.dita         ├── product-b-setup.dita
  └── common-maintenance.dita      └── common-maintenance.dita  ← REUSED
```

**To measure:** Cross-reference all `topicref/@href` values across all ditamaps.

---

## 4. Automated Reuse Calculation Script

### 4.1 Reuse Scanner Script

Save as `scripts/reuse-scanner.sh` in the repository root:

```bash
#!/bin/bash
# DITA Reuse Scanner v1.0
# Calculates content reuse metrics for the documentation repository

set -euo pipefail

echo "=== DITA Content Reuse Report ==="
echo "Date: $(date '+%Y-%m-%d %H:%M')"
echo "Repository: $(basename $(git rev-parse --show-toplevel 2>/dev/null || echo '.'))"
echo "Branch: $(git branch --show-current 2>/dev/null || echo 'N/A')"
echo ""

# 1. Count total topics
total_topics=$(find . -name "*.dita" -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "Total DITA topics: $total_topics"

# 2. Count conref usage
conref_count=$(grep -rh 'conref=' --include="*.dita" . \
    -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "Conref instances: $conref_count"

# 3. Count conkeyref usage
conkeyref_count=$(grep -rh 'conkeyref=' --include="*.dita" . \
    -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "Conkeyref instances: $conkeyref_count"

# 4. Count keyref usage (keyword + ph elements)
keyref_count=$(grep -rh 'keyref=' --include="*.dita" . \
    -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
echo "Keyref instances: $keyref_count"

# 5. Find topics referenced by multiple maps
echo ""
echo "=== Topics Referenced by Multiple Maps ==="
find . -name "*.ditamap" -not -path "*/output/*" | while read map; do
    grep -oP 'href="[^"]+\.dita"' "$map" | sed 's/href="//;s/"//'
done | sort | uniq -c | sort -rn | while read count topic; do
    if [ "$count" -gt 1 ]; then
        echo "  [$count maps] $topic"
    fi
done

# 6. Calculate reuse rate
echo ""
echo "=== Reuse Rate Calculation ==="
total_unique=$(find . -name "*.dita" -not -path "*/output/*" -not -path "*/templates/*" | wc -l)
reuse_instances=$((conref_count + conkeyref_count))
reuse_rate=$(echo "scale=2; ($reuse_instances * 100) / $total_unique" | bc 2>/dev/null || echo "N/A")

echo "Total unique topics: $total_unique"
echo "Total reuse instances (conref + conkeyref): $reuse_instances"
echo "Reuse Rate: ${reuse_rate}%"
echo ""

# 7. Weighted reuse (word count based)
echo "=== Weighted Reuse Rate ==="
# Identify reused topics (referenced by 2+ maps)
reused_topics=$(find . -name "*.ditamap" -not -path "*/output/*" | \
    xargs grep -ohP 'href="[^"]+\.dita"' 2>/dev/null | \
    sed 's/href="//;s/"//' | sort | uniq -d)

reused_words=0
all_words=0

while IFS= read -r topic; do
    [ -z "$topic" ] && continue
    # Resolve relative path
    resolved=$(find . -name "$(basename "$topic")" -not -path "*/output/*" 2>/dev/null | head -1)
    if [ -n "$resolved" ]; then
        wc_w=$(wc -w < "$resolved" 2>/dev/null || echo 0)
        reused_words=$((reused_words + wc_w))
    fi
done <<< "$reused_topics"

find . -name "*.dita" -not -path "*/output/*" -not -path "*/templates/*" | \
    while read f; do
        wc_w=$(wc -w < "$f" 2>/dev/null || echo 0)
        all_words=$((all_words + wc_w))
    done

if [ "$all_words" -gt 0 ]; then
    weighted_rate=$(echo "scale=2; ($reused_words * 100) / $all_words" | bc 2>/dev/null)
    echo "Reused topic word count: $reused_words"
    echo "Total word count: $all_words"
    echo "Weighted Reuse Rate: ${weighted_rate}%"
fi

echo ""
echo "=== End of Report ==="
```

### 4.2 Windows PowerShell Equivalent

Save as `scripts/reuse-scanner.ps1`:

```powershell
# DITA Reuse Scanner v1.0 (PowerShell)
Write-Host "=== DITA Content Reuse Report ===" -ForegroundColor Cyan
Write-Host "Date: $(Get-Date -Format 'yyyy-MM-dd HH:mm')"
Write-Host ""

$ditaFiles = Get-ChildItem -Recurse -Filter "*.dita" | Where-Object { $_.FullName -notmatch 'output|templates' }
$totalTopics = $ditaFiles.Count
Write-Host "Total DITA topics: $totalTopics"

$conrefCount = Select-String -Pattern 'conref=' -Include "*.dita" -Recurse | Where-Object { $_.Path -notmatch 'output|templates' } | Measure-Object | Select-Object -ExpandProperty Count
Write-Host "Conref instances: $conrefCount"

$keyrefCount = Select-String -Pattern 'keyref=' -Include "*.dita" -Recurse | Where-Object { $_.Path -notmatch 'output|templates' } | Measure-Object | Select-Object -ExpandProperty Count
Write-Host "Keyref instances: $keyrefCount"

$reuseRate = [math]::Round(($conrefCount * 100) / $totalTopics, 2)
Write-Host "Reuse Rate: $reuseRate%" -ForegroundColor Green
```

### 4.3 Scheduled Execution

| Frequency | Scope | Trigger |
|-----------|-------|---------|
| **Per commit** | Changed files only | Pre-commit hook |
| **Per PR** | All topics in feature branch | CI/CD PR check |
| **Weekly** | Full repository | Cron job (Monday 0800) |
| **Monthly** | Full repository + trend report | Scheduled pipeline |
| **Quarterly** | Full audit + optimization plan | Governance review cycle |

---

## 5. CI/CD Reuse Gate Integration

### 5.1 CI/CD Reuse Gate Script

Add to `scripts/reuse-gate.sh`:

```bash
#!/bin/bash
# Reuse Gate - validates reuse rate meets minimum threshold
# Usage: ./reuse-gate.sh [threshold]
# Example: ./reuse-gate.sh 15

set -euo pipefail

THRESHOLD="${1:-15}"

# Run reuse scanner
chmod +x scripts/reuse-scanner.sh
./scripts/reuse-scanner.sh > reuse-report.txt

# Extract reuse rate
RATE=$(grep 'Reuse Rate:' reuse-report.txt | grep -oP '\d+\.?\d*')

if [ -z "$RATE" ]; then
    echo "ERROR: Could not extract reuse rate from report"
    exit 1
fi

echo "Current reuse rate: ${RATE}%"

# Gate check
if (( $(echo "$RATE < $THRESHOLD" | bc -l) )); then
    echo "FAIL: Reuse rate ${RATE}% is below ${THRESHOLD}% threshold"
    exit 1
else
    echo "PASS: Reuse rate ${RATE}% meets minimum threshold of ${THRESHOLD}%"
fi
```

### 5.2 Gate Thresholds by Phase

| Phase | Minimum Threshold | Warning Threshold | Action |
|-------|------------------|-------------------|--------|
| **Month 1-3** | No gate | < 5% | Flag regression |
| **Month 4-6** | 10% | < 8% | Block PR |
| **Month 7-9** | 20% | < 15% | Block PR |
| **Month 10-12** | 30% | < 25% | Block PR |
| **Production** | 35% | < 30% | Block PR, escalate |

---

## 6. Reuse Opportunity Identification

### 6.1 Duplicate Content Detection

Run periodically to find topics with similar content:

```bash
#!/bin/bash
# Find potential duplicate topics using similarity heuristics

echo "=== Duplicate Content Candidates ==="

# Method 1: Same word count (within 10%)
find . -name "*.dita" -not -path "*/output/*" | while read f1; do
    w1=$(wc -w < "$f1")
    find . -name "*.dita" -not -path "*/output/*" -not -name "$(basename "$f1")" | while read f2; do
        w2=$(wc -w < "$f2")
        diff=$((w1 > w2 ? w1 - w2 : w2 - w1))
        if [ "$diff" -lt 5 ] && [ "$w1" -gt 0 ]; then
            ratio=$(echo "scale=2; $diff / $w1 * 100" | bc)
            if [ "$(echo "$ratio < 10" | bc)" -eq 1 ]; then
                echo "  CHECK: $(basename "$f1") ~ $(basename "$f2")"
            fi
        fi
    done
done
```

### 6.2 Reuse Opportunity Checklist

Use this checklist during content planning and review:

**Before Writing New Content:**
- [ ] Searched shared library for existing content on this subject
- [ ] Searched by key terms in metadata (product, component, audience)
- [ ] Checked if existing content can be extended via `conref`
- [ ] Verified no similar procedure exists in another product's topic set

**During Topic Review:**
- [ ] Identified procedural steps duplicated across topics
- [ ] Identified warnings and cautions that could be centralized
- [ ] Identified specifications that could be moved to keyref variables
- [ ] Identified boilerplate text (introductions, conclusions) for conref

**During Map Review:**
- [ ] Checked if this topic is already referenced from another map
- [ ] Considered using `topicgroup` for shared navigation structures
- [ ] Validated that key definitions cover all reused values

### 6.3 High-Value Reuse Candidates

| Content Type | Reuse Potential | Priority | Typical ROI |
|-------------|----------------|----------|-------------|
| Safety warnings / hazards | Very High | P0 | 15-20% reuse gain |
| Standard procedures (setup, shutdown) | High | P0 | 10-15% |
| Specifications tables | High | P1 | 8-12% |
| Legal boilerplate | Very High | P0 | 5-8% |
| Introduction / overview text | Medium | P1 | 3-5% |
| Troubleshooting steps | Medium | P2 | 2-5% |
| Glossary definitions | Very High | P0 | 5-10% |
| Maintenance schedules | Low | P2 | 1-3% |

---

## 7. Reuse Optimization Workflow

### Phase 1: Audit (Week 1 of each quarter)
1. Run reuse scanner on full repository
2. Identify bottom-10 topics with lowest reuse contribution
3. Flag duplicate content candidates
4. Generate reuse opportunity report

### Phase 2: Plan (Week 2)
1. Review report in monthly DGC meeting
2. Prioritize candidates by effort vs. impact
3. Assign optimization tasks to topic owners
4. Set quarterly reuse rate target

### Phase 3: Execute (Weeks 3-8)
1. Authors consolidate duplicate content into shared library
2. Convert hardcoded values to keyref variables
3. Create conref targets for reusable fragments
4. Update maps to reference shared topics

### Phase 4: Validate (Week 9)
1. Run reuse scanner to measure improvement
2. Verify no broken references (run full build)
3. Check all maps still produce correct output
4. Document lessons learned

### Phase 5: Report (Week 10)
1. Generate quarterly reuse report
2. Present to DGC with trend data
3. Update targets for next quarter
4. Recognize top contributors

---

## 8. Reporting & Dashboard

### 8.1 Quarterly Reuse Report Template

```markdown
# Quarterly Reuse Report — Qx 2026

## Summary
| Metric | This Quarter | Last Quarter | Change | Target |
|--------|-------------|-------------|--------|--------|
| Simple Reuse Rate | XX% | XX% | +/-X% | XX% |
| Weighted Reuse Rate | XX% | XX% | +/-X% | XX% |
| Conref Instances | XXX | XXX | +/-X | — |
| Keyref Instances | XXX | XXX | +/-X | — |
| Reused Topics | XX | XX | +/-X | — |
| Total Topics | XXX | XXX | +/-X | — |

## Top Reused Content (This Quarter)
1. [Topic Title] — referenced by X maps
2. [Topic Title] — referenced by X maps
3. [Topic Title] — referenced by X maps

## Optimization Actions Completed
- [x] Consolidated X duplicate topics
- [x] Converted X values to keyref variables
- [x] Created X new shared library entries
- [ ] Target XX% reuse by end of next quarter

## Recommendations
1. 
2. 
3. 
```

### 8.2 Trend Tracking

Track these metrics over time to demonstrate improvement:

| Quarter | Date | Reuse Rate | Weighted Rate | Conref Count | Keyref Count | Total Topics |
|---------|------|-----------|--------------|-------------|-------------|-------------|
| Q1 2026 | 2026-03-31 | | | | | |
| Q2 2026 | 2026-06-30 | | | | | |
| Q3 2026 | 2026-09-30 | | | | | |
| Q4 2026 | 2026-12-31 | | | | | |

---

## 9. Targets & Benchmarks

### 9.1 Annual Targets

| Year | Minimum | Target | Stretch |
|------|---------|--------|---------|
| Year 1 | 15% | 25% | 35% |
| Year 2 | 25% | 35% | 45% |
| Year 3 | 30% | 40% | 50% |

### 9.2 Team-Level Targets

| Team | Role | Reuse Contribution Target |
|------|------|--------------------------|
| Technical Writers | Create reusable content | 30% of authored topics are reused |
| Content Architects | Design shared library | 80% of shared library topics are reused |
| Product Managers | Identify duplicate content | Quarterly audit participation |
| Localization Team | Report reuse gaps | Identify 5+ opportunities per quarter |

---

## Related Documents

| Document | Location |
|----------|----------|
| Content Architecture | [ARCHITECTURE/01-CONTENT-ARCHITECTURE.md](../ARCHITECTURE/01-CONTENT-ARCHITECTURE.md) |
| Variable Management | [ARCHITECTURE/06-VARIABLE-MANAGEMENT.md](../ARCHITECTURE/06-VARIABLE-MANAGEMENT.md) |
| Shared Library Governance | [WORKFLOWS/04-SHARED-LIBRARY-GOVERNANCE.md](04-SHARED-LIBRARY-GOVERNANCE.md) |
| Quality Checklist | [STYLE-GUIDE/03-QUALITY-CHECKLIST.md](../STYLE-GUIDE/03-QUALITY-CHECKLIST.md) |
| CI/CD Pipeline | [CI-CD/01-CICD-PIPELINE.md](../CI-CD/01-CICD-PIPELINE.md) |
