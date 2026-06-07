---
marp: true
theme: uncover
class:
  - lead
  - invert
paginate: true
header: "Example Manufacturing — MC-5000 Training Program"
footer: "Confidential — For Authorized Personnel Only"
---

<!-- _class: lead invert -->
# MC-5000 Vertical Machining Center
## Operator &amp; Maintenance Training Program

**Module 2: Hands-on Maintenance &amp; Troubleshooting**

Example Manufacturing Co., Ltd.
Version 2.1.A — June 2026

---

## Module 2 Objectives

By the end of this session, you will be able to:

- **Perform** spindle laser alignment (full procedure)
- **Interpret** alignment measurement results
- **Execute** daily, weekly, and monthly maintenance tasks
- **Identify** common error codes on the SINUMERIK ONE HMI
- **Apply** LOTO (Lockout/Tagout) correctly
- **Record** maintenance logs in the system

> **Prerequisite:** Module 1 — Documentation &amp; Safety Awareness
> **Duration:** 8 hours (hands-on, on the machine)

---

## Before We Start — Safety Reminder

> **⚠️ Mandatory PPE for this module:**
> - Safety glasses with side shields
> - Steel-toed boots
> - Close-fitting work gloves (when handling tools)
> - Hearing protection (during spindle test runs)

**LOTO Rule:** Before any maintenance, verify:
1. Main disconnect switch is OFF
2. Personal padlock applied
3. Tag with your name and date attached
4. Verify zero energy state — try the start button

---

## Tool Kit Checklist

For today's exercises, verify your kit contains:

| Tool | P/N | Quantity | Checked |
|------|-----|----------|---------|
| Haimer Ziegler laser kit | HZ-5000-KIT | 1 | ☐ |
| Torque wrench (5–50 Nm) | TW-50-CAL | 1 | ☐ |
| Cleaning solvent | SOLV-100 | 1 can | ☐ |
| Lint-free wipes | WIPE-LF-100 | 1 pack | ☐ |
| Dial indicator + mag base | DI-10-MAG | 1 | ☐ |
| Hex key set (1.5–10 mm) | HEX-SET-12 | 1 | ☐ |
| Dead-blow hammer | DBH-2LB | 1 | ☐ |
| Digital caliper (0–150 mm) | DC-150 | 1 | ☐ |

> Found any missing tools? Report to the tool crib before starting.

---

## Exercise 1: Spindle Laser Alignment

**Reference task:** `SPINDLE-ALIGNMENT-TASK.dita`

We will walk through each step on the machine together:

```
Step 1: Prepare machine ─────────── LOTO → clean taper → mount emitter
Step 2: Mount target ────────────── Clean T-slot → mount bracket → align → torque
Step 3: Perform measurement ─────── Maintenance Mode → laser alignment screen
Step 4: Interpret & adjust ──────── Decision table → fine adjustment
Step 5: Verify ──────────────────── Re-measure → log result → test run
```

> **Instructor will demonstrate Step 1–2 first. Students repeat.**

---

## Decision Table — Spindle Offset

When you see these values on the laser controller:

| Offset | Status | Action |
|--------|--------|--------|
| **≤ ±3 µm** | ✅ Within spec | No adjustment needed. Verify and log. |
| **±3 to ±10 µm** | ⚠️ Minor | Fine-adjustment screws — 1/4 turn increments. Re-measure after each. |
| **> ±10 µm** | ❌ Major | Stop. Full mechanical alignment needed. See maintenance manual section 4.3. |

> **⚠️ Caution:** Never exceed 1/2 turn on any single adjustment screw without re-measuring.

---

## Exercise 1 — Common Mistakes

| Mistake | Consequence | Prevention |
|---------|-------------|------------|
| Skipping the 30-min cool-down | Thermal expansion skews results | Set a timer before starting |
| Dirty spindle taper | False offset reading | Clean with solvent until wipes come out clean |
| Overtightening target bracket | Bent bracket → false reading | Use torque wrench at 35 Nm |
| Not zeroing laser controller | 0.5–2 µm systematic error | Press "Zero Reference" before first measurement |

> **Remember:** Accuracy is ±3 µm — that's 0.003 mm. Small mistakes matter.

---

## Exercise 2: Daily Maintenance Routine

Perform these checks **at the start of every shift** (≈ 10 minutes):

1. **Coolant level** — Top up if below 70%
2. **Air pressure** — Verify 0.5–0.7 MPa on the regulator
3. **Lubrication oil level** — Check sight glass on the lubrication unit
4. **Chip tray** — Clear chips if > 50% full
5. **Safety interlocks** — Test each door interlock and E-Stop
6. **Spindle warm-up** — Run warm-up cycle (HMI: `Maintenance > Warm-Up`)

> Log completion in HMI: `Maintenance > Daily Checklist`

---

## Exercise 3: Weekly Maintenance

Every **40 operating hours** or weekly:

| Item | Detail |
|------|--------|
| **Coolant concentration** | Check refractometer: 5–8% | |
| **Filter cleaning** | Clean coolant tank return filter | |
| **Ball screw lubrication** | Apply grease to X/Y/Z ball screws | |
| **Way wiper inspection** | Check way wipers for wear or damage | |
| **Air dryer** | Drain condensation from air dryer bowl | |
| **Electrical cabinet** | Check fan filters — clean or replace | |

> **Reference:** `MC5000-MAINTENANCE-SCHEDULE.dita` (Reference topic)

---

## Exercise 4: Monthly Maintenance

Every **160 operating hours** or monthly:

| Item | Detail | Tools Needed |
|------|--------|-------------|
| **Spindle taper inspection** | Check for scoring/wear | Magnifying glass, solvent |
| **Tool clamp force test** | Verify drawbar force > 12 kN | Drawbar force gauge |
| **Axis backlash check** | Measure X/Y/Z backlash | Dial indicator |
| **Coolant tank cleaning** | Drain, clean, refill | Pump, waste container |
| **Way cover inspection** | Check telescopic covers for damage | Flashlight |
| **Electrical connection check** | Tighten loose terminals | Screwdriver set |

> Log all measurements in the HMI maintenance logbook.

---

## Reading Error Codes on SINUMERIK ONE

Common alarms you may encounter:

| Code | Meaning | Likely Cause | Action |
|------|---------|-------------|--------|
| **700001** | Spindle overload | Heavy cut or worn tool | Reduce feed rate. Check tool condition. |
| **700016** | Coolant pressure low | Clogged filter or pump fault | Clean filter. Check pump. |
| **700022** | X-axis following error | Mechanical binding or encoder | Reduce feed. Check ball screw. |
| **700045** | Door interlock open | Door not fully closed | Close and verify interlock. |
| **700060** | Lubrication fault | Low oil or pump failure | Check oil level. Prime pump. |

> For full list: HMI `Diagnostics > Alarm List > Help`

---

## Exercise 5: Troubleshooting Scenario

**Scenario:** During a production run, the machine stops with alarm **700016** — "Coolant pressure low." The operator restarts but the alarm returns after 2 minutes.

**Group discussion — 10 min:**

1. What is the **most likely** root cause?
2. What **safety precautions** apply before inspecting?
3. What **documentation** would you consult?
4. What is the **correct repair procedure**?
5. When should you **escalate** to maintenance engineering?

> Reference: `MC5000-COOLANT-SYSTEM.dita` (Concept) and `MC5000-FILTER-REPLACEMENT.dita` (Task)

---

## Exercise 5 — Answer Guide

1. **Likely root cause:** Coolant tank return filter is clogged
2. **Safety:** LOTO the coolant pump circuit before opening the tank
3. **Documentation:** Coolant System concept topic → Filter Replacement task topic
4. **Procedure:**
   - LOTO pump circuit
   - Open tank hatch (Zone 4 — key-lock reset required)
   - Remove and clean filter element
   - Reassemble, remove LOTO, restart
   - Verify pressure on HMI
5. **Escalate if:** Pressure still low after filter cleaning — possible pump impeller damage

---

## Maintenance Logging

Every maintenance action must be logged:

**HMI path:**
```
Maintenance > Log > New Entry
├── Date: (auto-filled)
├── Operator: (your badge ID)
├── Machine hours: (auto-filled)
├── Work type: [Daily | Weekly | Monthly | Repair | Calibration]
├── Description: (free text — be specific)
├── Parts replaced: (if any — include P/N)
└── Signature: (digital sign-off)
```

> **Why it matters:** Audit trail for ISO 9001 / ISO 14001 compliance. Incomplete logs = non-compliance.

---

## Knowledge Check

1. What is the **maximum offset** allowed before declaring "Major misalignment"?
2. List **three** items on the daily maintenance checklist.
3. What does alarm code **700045** mean?
4. Before opening the coolant tank hatch, what must you do?
5. Where is the **lubrication oil sight glass** located?

> Write your answers. Review together in 10 minutes.

---

<!-- _class: lead invert -->
## Answers

1. **> ±10 µm** — requires full mechanical alignment
2. Coolant level, air pressure, oil level, chip tray, interlocks, spindle warm-up
3. **700045** — Door interlock open (door not fully closed)
4. **LOTO** the coolant pump circuit — Zone 4 requires key-lock reset
5. On the **lubrication unit**, typically on the right side of the machine column

---

## Module 2 — Sign-off Checklist

| Task | Completed |
|------|-----------|
| Spindle laser alignment — demonstrated | ☐ |
| Daily maintenance routine — performed independently | ☐ |
| Weekly maintenance items — identified | ☐ |
| Error code lookup — practiced on HMI | ☐ |
| Troubleshooting scenario — completed | ☐ |
| Maintenance log entry — created | ☐ |

> **Next:** Module 3 — Advanced Diagnostics & Multi-Axis Programming

---

<!-- _class: lead invert -->
## Thank You

**Example Manufacturing Co., Ltd.**

Training Department — Gao Ling
Maintenance Lead — Xu Feng

*"Precision maintenance = precision machining."*

---

## Appendix: Quick Maintenance Reference

| Task | Interval | Topic Type | Topic Name |
|------|----------|------------|------------|
| Spindle alignment | 1,000 hrs | Task | Spindle Laser Alignment |
| Coolant filter clean | Weekly | Task | Filter Replacement |
| Way cover inspect | Monthly | Task | Way Cover Inspection |
| Spindle taper inspect | Monthly | Task | Spindle Taper Inspection |
| Axis backlash check | Monthly | Task | Axis Backlash Measurement |
| Lubrication top-up | Daily | Reference | Lubrication Schedule |
| Full maintenance | Annually | Task | Annual Overhaul Procedure |
