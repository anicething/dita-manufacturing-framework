# Publishing SLA — Timelines and Metrics

**Document ID:** CIC-003  
**Version:** 1.0  
**Last Updated:** 2026-06-07  
**Status:** Approved  

---

## 1. SLA Targets

### 1.1 End-to-End Publishing SLA

| Step | Target | Owner | Automation |
|------|--------|-------|-----------|
| Content commit to main | Immediate | Writer | Manual |
| CI/CD trigger | < 1 min | CI/CD | Automated |
| XML validation | < 1 min | CI/CD | Automated |
| HTML5 generation | < 15 min | DITA-OT | Automated |
| PDF generation | < 20 min | DITA-OT | Automated |
| Mobile (EPUB) generation | < 10 min | DITA-OT | Automated |
| Deploy to target | < 5 min | CI/CD | Automated |
| Search indexing | < 10 min | Search Engine | Automated |
| **Total (single product)** | **< 1 hour** | **Pipeline** | **100% automated** |

### 1.2 Multi-Product SLA

| Products | HTML5 | PDF | Mobile | Total |
|----------|-------|-----|--------|-------|
| 1 product | < 15 min | < 20 min | < 10 min | < 1 hour |
| 3 products (parallel) | < 20 min | < 25 min | < 15 min | < 1 hour |
| 5 products (parallel) | < 30 min | < 40 min | < 20 min | < 1.5 hours |

---

## 2. SLA Measurement

### 2.1 Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Pipeline success rate | > 98% | Successful runs / total runs |
| Average pipeline duration | < 45 min | Mean of last 30 runs |
| P50 validation time | < 30 sec | Median validation duration |
| P95 publishing time | < 60 min | 95th percentile of all runs |
| Deployment success rate | 100% | Deployments with verified health check |
| Rollback time | < 15 min | Time from decision to live rollback |

### 2.2 Health Check Script

```bash
#!/bin/bash
# publish-health.sh - Check publishing SLA health
# Run as a scheduled task (weekly)

echo "=== Publishing SLA Health Report ==="

TOTAL=$(grep -c '.' /var/log/publish/pipeline.log 2>/dev/null || echo 0)
SUCCESS=$(grep -c 'BUILD_SUCCESS' /var/log/publish/pipeline.log 2>/dev/null || echo 0)
FAILURES=$((TOTAL - SUCCESS))

if [ "$TOTAL" -gt 0 ]; then
    RATE=$(echo "scale=1; $SUCCESS * 100 / $TOTAL" | bc)
    echo "Pipeline success rate: ${RATE}%"
else
    echo "No pipeline runs recorded"
fi

echo "Total runs: $TOTAL"
echo "Successful: $SUCCESS"
echo "Failed: $FAILURES"
```

---

## 3. SLA Breach Procedures

### 3.1 Breach Definition

| Level | Criteria | Response |
|-------|----------|----------|
| **Warning** | > 60 minutes but < 90 minutes | Investigate, no escalation |
| **Minor** | > 90 minutes | Investigate + report to team lead |
| **Major** | > 2 hours or consecutive failures | Escalate to IT lead + CoE |
| **Critical** | Pipeline down > 4 hours | Emergency response procedure |

### 3.2 Response Time Targets

| Severity | Initial Response | Status Update | Resolution |
|----------|-----------------|---------------|------------|
| Warning | N/A | N/A | Next business day |
| Minor | 2 hours | Daily | 2 business days |
| Major | 1 hour | Every 4 hours | 1 business day |
| Critical | 15 minutes | Every hour | 4 hours |

---

## 4. SLA Reporting

### 4.1 Monthly Report Template

| Metric | Target | Current Month | Previous Month | Trend |
|--------|--------|--------------|----------------|-------|
| Pipeline success rate | > 98% | | | |
| Avg duration | < 45 min | | | |
| P95 duration | < 60 min | | | |
| Deployment success | 100% | | | |
| Rollback time | < 15 min | | | |

### 4.2 Quarterly Review

Include SLA performance in the quarterly governance review:

1. Compare current vs. previous quarter metrics
2. Identify recurring failure patterns
3. Plan infrastructure improvements
4. Update SLA targets if needed

---

## Related Documents

| Document | Location |
|----------|----------|
| CI/CD Pipeline | [01-CICD-PIPELINE.md](01-CICD-PIPELINE.md) |
| DITA-OT Configuration | [02-DITA-OT-CONFIG.md](02-DITA-OT-CONFIG.md) |
| Risk Control Strategy | [RISK-CONTROL-STRATEGY.md](../RISK-CONTROL-STRATEGY.md) |

---

**Document ID:** CIC-003  
**Next Review Date:** Q3 2026
