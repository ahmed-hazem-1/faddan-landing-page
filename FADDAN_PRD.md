# Faddan AI Robotics — Product Requirements Document (PRD)

**Product:** Faddan Smart Robot Dashboard & Control System  
**Version:** 1.0  
**Date:** November 18, 2026  
**Authors:** Faddan AI Robotics Team  
**Status:** Approved for Implementation  

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Product Vision & Goals](#3-product-vision--goals)
4. [Target Users & Personas](#4-target-users--personas)
5. [System Architecture & Constraints](#5-system-architecture--constraints)
6. [Functional Requirements](#6-functional-requirements)
7. [Non-Functional Requirements](#7-non-functional-requirements)
8. [Prerequisites & Dependencies](#8-prerequisites--dependencies)
9. [Out of Scope](#9-out-of-scope)
10. [Assumptions](#10-assumptions)
11. [Data Structures & Mock Data Reference](#11-data-structures--mock-data-reference)
12. [Glossary](#12-glossary)

---

## 1. Executive Summary

**Faddan AI Robotics** is an autonomous under-canopy spot-spraying and inspection system designed for commercial strawberry greenhouses in Egypt. The system deploys a 4WD rover equipped with a YOLOv11-seg computer vision model running on an NVIDIA Jetson Orin Nano to crawl beneath the plant canopy across 12 crop rows (each 100 meters long), detect foliar diseases at the individual leaf level, and apply precision micro-dose pesticide bursts (10–22 mL per infected leaf) only to confirmed disease hotspots.

The product delivers a **78.6% reduction in chemical usage** (3.2 L vs. 15.0 L per greenhouse per spray cycle), enabling **zero pesticide residue** compliance for international export under the **GlobalG.A.P** standard. The system operates entirely **offline** within the greenhouse using a local 2.4 GHz Wi-Fi access point, with optional cloud synchronization when the agronomist returns to the farm office.

The dashboard is a bilingual (Arabic RTL / English LTR), dual-theme (Daylight Mode for field use, Dark Mode for investor presentations) web application that provides 6 screens: 4 core MVP screens and 2 optional diagnostic add-ons.

---

## 2. Problem Statement

Egyptian strawberry exporters face three compounding challenges:

1. **Chemical over-application:** Traditional blanket spraying consumes 15 liters of diluted fungicide per greenhouse per spray cycle, applied uniformly across all 12 rows regardless of actual infection levels.
2. **Export rejection risk:** Excessive pesticide residue causes produce to fail Pre-Harvest Interval (PHI) compliance checks and GlobalG.A.P audits, blocking lucrative EU and Gulf export channels.
3. **No connectivity infrastructure:** Commercial greenhouses in Egypt's agricultural zones (e.g., Ismailia, Beheira, Sharqia governorates) lack reliable internet or cellular coverage, making cloud-dependent IoT solutions impractical.

---

## 3. Product Vision & Goals

| Goal | Metric | Target |
|------|--------|--------|
| Reduce chemical usage per spray cycle | Liters of diluted pesticide per greenhouse | 3.2 L (from 15.0 L baseline) = **78.6% reduction** |
| Ensure export-grade produce | PHI compliance rate across all rows | **100% Zero-Residue** |
| Operate without internet | Percentage of functionality available offline | **100% core functionality** |
| Scan full greenhouse in one mission | Total scan time for 12 rows × 100m | **≤ 48 minutes** at 0.4 m/s |
| Improve AI accuracy over time | Agronomist-verified correct detections | **> 90% confidence threshold** |
| Maximize greenhouse health | Healthy row segments out of 36 total | **≥ 32 (93.6%)** |

---

## 4. Target Users & Personas

| Persona | Role | Primary Screens | Key Need |
|---------|------|-----------------|----------|
| **Farm Owner / Investor** | Financial decision-maker | Screen 1 (ROI Summary) | See EGP savings, season projections, export readiness |
| **Agronomist / Field Engineer** | On-site plant health expert | Screen 2 (100m Map & AI Inspector), Screen 6 (AI Review Queue) | Verify AI diagnoses, correct false positives, approve treatments |
| **Rover Operator / Technician** | Field robot operator | Screen 3 (Quick Control), Screen 5 (Hardware Diagnostics) | Start/pause missions, troubleshoot motor/nozzle faults |
| **Farm Manager** | Harvest logistics coordinator | Screen 4 (Yield Forecast & PHI Safety) | Plan harvest schedules, manage PHI locks per row |

---

## 5. System Architecture & Constraints

### 5.1 Hardware Platform

| Component | Specification |
|-----------|--------------|
| Compute Unit | NVIDIA Jetson Orin Nano |
| AI Model | YOLOv11-seg (instance segmentation) |
| Drivetrain | 4WD independent motors (FL, FR, RL, RR) with VESC controllers |
| Motor Communication | CAN-Bus with Hall Sensor feedback per motor |
| Battery | LiFePO4 pack (monitored percentage, ~84% nominal) |
| Spray System | 3 solenoid-actuated nozzles, peristaltic pump, 15L tank |
| Spray Verification | Under-canopy camera visual confirmation (mist detection) |
| Obstacle Detection | Front-facing Time-of-Flight (ToF) sensor |
| Localization | Visual Odometry + IMU + GPS fusion |
| Microcontroller | ESP32 (UART/CAN heartbeat at 100ms intervals) |

### 5.2 Network Architecture (Offline-First)

| Layer | Description |
|-------|-------------|
| **Local Network** | 2.4 GHz outdoor access point mounted at greenhouse entrance; covers all 12 rows; no SIM card or internet required |
| **Image Compression** | Infected leaf images auto-compressed to ~90 KB with metadata tags (Row ID + meter position) |
| **Local Storage** | Full mission data stored on rover and field station (phone/tablet) |
| **Cloud Sync** | One-click sync button uploads accumulated data when agronomist returns to office network |

### 5.3 UI/UX Constraints

| Constraint | Specification |
|------------|--------------|
| **Bilingual** | Arabic (RTL) and English (LTR) with real-time toggle; `dir` attribute switches automatically |
| **Font** | Cairo (Google Fonts) for authentic Arabic rendering |
| **Dual Theme** | Daylight Mode (high-contrast for direct sunlight) and Dark Mode (for investor presentations and office analysis) |
| **Persistent E-Stop** | Emergency Stop button always visible in top header bar; locks all motors and spray pump immediately |
| **Touch-Friendly** | All field controls (Start, Pause, Return to Dock) are large touch targets suitable for gloved hands |
| **Field-First Design** | Status bar shows only critical telemetry (connection, location, battery, tank) to minimize cognitive load under sun |

### 5.4 Operational Constraints

| Constraint | Value |
|------------|-------|
| Rover speed | 0.4 m/s during scan |
| Micro-dose range | 10–22 mL per infected leaf |
| Average dose | 12 mL per leaf |
| Total diluted chemical per greenhouse scan | 3.2 L |
| Traditional blanket spray baseline | 15.0 L per greenhouse |
| PHI auto-lock threshold | ≤ 3 days before harvest |
| AI low-confidence threshold | < 75% (routed to Human-in-the-Loop queue) |
| Camera stream (on-demand) | 28 FPS over local Wi-Fi |
| Smart snapshot mode (default) | Still images only on detection (~90 KB each) |
| Nozzle purge test pulse | 200 ms |
| Rover stuck detection window | > 5 seconds of zero displacement at > 0 RPM |
| Jetson thermal throttle threshold | 78°C |
| Battery critical threshold | < 15% triggers auto Return-to-Home |

---

## 6. Functional Requirements

### 6.1 Persistent Top Status Telemetry Bar

| ID | Requirement | Details |
|----|-------------|---------|
| FR-01 | Display connection mode pill | Toggle between "Local Wi-Fi 2.4GHz (No Internet)" and "Cloud Synced (Office Network)"; animated ping indicator; clickable to simulate mode switch |
| FR-02 | Display rover real-time location | Format: "Row X (@ Y.Ym)" — e.g., "Row 4 (@ 63.2m)" |
| FR-03 | Display battery level | LiFePO4 percentage with icon (e.g., "Battery: 84%") |
| FR-04 | Display tank level | Percentage and liters (e.g., "Tank: 68% (10.2L)") |
| FR-05 | Emergency Stop button | Toggle E-Stop on/off; when engaged: all motors and spray pump locked, full-width red banner appears stating lockout status and row location; button pulses amber when engaged |
| FR-06 | Theme toggle | Switch between Daylight (Sun) and Dark (Moon) modes |
| FR-07 | Language toggle | Switch between Arabic (RTL) and English (LTR); all labels, descriptions, and content switch atomically |

### 6.2 Screen 1 — Executive Overview & ROI Summary (`mvp-overview`)

| ID | Requirement | Details |
|----|-------------|---------|
| FR-10 | Interactive ROI Calculator (expandable drawer) | 3 slider inputs: (a) Number of greenhouses (1–20, default: 1), (b) Sprays per season (6–30, default: 18), (c) Concentrated pesticide cost per liter (default: 1,850 EGP). Formula: `savedLiters = (15 - 3.2) × sprays × greenhouses`; `savedEGP = savedLiters × (costPerLiter / 9.2)`. Display projected seasonal savings in EGP prominently |
| FR-11 | KPI Card 1: Chemical & Cost Savings | Display "78.6% Saved" + calculated EGP savings per season; subtitle: spot-spraying vs. blanket spraying |
| FR-12 | KPI Card 2: Greenhouse Health Score | Display "93.6%" with "+4.2% Improved" badge; "32 of 36 Row Segments Healthy" |
| FR-13 | KPI Card 3: Rows Scanned Today | Display "12 / 12 Rows Done"; "13 Hotspots Spot-Sprayed"; "48 mins at 0.4 m/s" |
| FR-14 | KPI Card 4: Actual Spray Used Today | Display "3.2 L" with strikethrough "15.0 L Trad."; "Export Ready (Zero Residue Grade)"; "12ml micro-burst per infected leaf" |
| FR-15 | Row-by-Row mL Usage Bar Chart | 12-column dual-bar chart: gray bar = traditional (1250 mL fixed), green/amber bar = Faddan actual mL (amber if > 350 mL). Permanent top badge per column showing: savings % (e.g., "-88%"), strikethrough old amount, new amount in mL. Hover tooltips on each bar. Click row label navigates to Screen 2 with that row's first pinpoint selected |
| FR-16 | Detected Diseases Donut Chart | SVG donut showing 18 total spots: Botrytis Gray Mold 52% / 9 spots (rose #f43f5e), Powdery Mildew 33% / 6 spots (amber #fbbf24), Spider Mites 15% / 3 spots (sky #38bdf8). Center label: "18 Total Spots". Color-coded legend below |

### 6.3 Screen 2 — 100m Greenhouse Map, AI Inspector & Micro-Climate Heatmaps (`mvp-rows`)

| ID | Requirement | Details |
|----|-------------|---------|
| FR-20 | Filter buttons for pinpoints | 4 options: All Spots (21), Critical (Red), Suspicion (Yellow), Sprayed (Blue) |
| FR-21 | Linear 100m Row Schematic (12 rows) | Each row displayed as a horizontal track from 0m to 100m with tick marks at 20m intervals (20m, 40m, 60m, 80m, 100m). Rover icon (🤖) shown at current position (Row 4, 63.2m). Colored pinpoint dots placed at their exact meter positions. Verification badges (✓ for approved, ✗ for corrected) displayed above each pinpoint |
| FR-22 | Pinpoint & AI Inspector panel | Clicking a pinpoint opens: simulated infected leaf image with YOLOv11-seg overlay mask (toggleable via Show/Hide AI Mask button). Data card showing: disease name (bilingual), confidence %, capture time, compressed image size (KB), spray dose (mL). Row/meter/segment context |
| FR-23 | Inline Agronomist Feedback loop | "Correct ✓" button: marks pinpoint as `approved`, adds ✓ badge on map, feeds into training data. "Wrong ✗" button: opens correction dropdown with 4 alternative labels: (1) Healthy Leaf (Light Reflection), (2) Calcium Deficiency / Tip Burn, (3) Powdery Mildew, (4) Spider Mites. Can re-edit at any time |
| FR-24 | Temperature Heatmap (°C) | Interactive canvas with `onMouseMove` tracking; displays probed coordinates (Row, Meter, °C). Hot zone: top-left (Rows 1–3, 0–25m) peaking at 33.8°C. Color scale: 22°C (cool green) → 35°C (heat stress red). Computed via mathematical model: `baseLine + hotBoost - midCool` |
| FR-25 | Humidity Heatmap (%) | Interactive canvas with `onMouseMove` tracking; displays probed coordinates (Row, Meter, %). Critical zone: bottom-right (Rows 10–12, 75–100m) peaking at 89.5%. Color scale: 55% (optimal blue) → 90%+ (fungal risk red). Computed via mathematical model |
| FR-26 | Spatial Climate Alerts (3 alerts) | Alert 1: "Open north side curtain and activate cooling fan #1" (heat zone). Alert 2: "Increase ventilation and reduce drip irrigation at Rows 10–12 end" (humidity zone). Alert 3: "Mid-greenhouse zone is in optimal climate for strawberry fruit set" (healthy zone) |

### 6.4 Screen 3 — Quick Control & Offline Sync (`mvp-control`)

| ID | Requirement | Details |
|----|-------------|---------|
| FR-30 | Mission control buttons | 3 large touch-friendly buttons: (a) Start / Resume scanning, (b) Pause Rover, (c) Return to Dock (charging + refill station). Buttons update `robotState` ('scanning' / 'paused' / 'returning') |
| FR-31 | Local-to-Cloud Sync Engine | Displays count of unsynced images (e.g., "14 images pending"). Visual progress bar (15% → 100%) with animation. Sync button triggers simulated upload sequence (increments by 25% every 450ms). On completion: unsynced count resets to 0, connection mode switches to "Cloud Synced" |
| FR-32 | Export Compliance PDF Report button | Opens modal (see FR-50) for generating the GlobalG.A.P spray audit certificate |
| FR-33 | On-Demand Local Video Stream | Default: Smart Snapshot Mode (still images only on detection, ~90 KB). Toggle: Live Stream Mode (28 FPS video feed over local Wi-Fi). Visual indicator showing active mode |

### 6.5 Screen 4 — Yield Forecast & PHI Safety Lock (`mvp-harvest-phi`)

| ID | Requirement | Details |
|----|-------------|---------|
| FR-40 | Aggregate harvest KPIs | 4 summary cards: (a) Ripe red strawberries ready for harvest: 486 kg (~24,300 fruits), (b) Turning pink + green (next week's batch): 864 kg (390 kg turning + 474 kg green), (c) PHI-locked rows count out of 12, (d) GlobalG.A.P compliance: 100% Zero-Residue Safe |
| FR-41 | Unified 12-Row Ripeness & Safety Grid | Per-row card containing: Row name + harvest countdown ("Harvest in X days"), total projected yield (kg), stacked color bar (red = ripe, pink = turning, green), individual kg values per stage |
| FR-42 | Per-row PHI Lock toggle | Automatic: rows with ≤ 3 days to harvest are auto-locked (chemical spraying disabled via hardware lock). Manual override: toggle button to Lock / Unlock any row. Locked rows display lock icon and "PHI Locked" badge |

### 6.6 Screen 5 — Smart Hardware Fault Diagnostics (`opt-hardware`)

| ID | Requirement | Details |
|----|-------------|---------|
| FR-50 | Active fault alert banner | Displays count of active faults, fault location (row + meter), required technician action, and "Resolved ✓" dismiss button |
| FR-51 | 4WD Chassis & Sprayer Visualizer | Schematic showing all 4 motors (FL, FR, RL, RR) with real-time: current draw (Amps), RPM, Hall Sensor status. Jetson Orin CPU status. Battery percentage. Pump system status. Wheel RPM vs. Visual Odometry comparison for slip detection |
| FR-52 | Nozzle Purge Test buttons | 3 buttons (Nozzle 1, 2, 3) each firing a 200ms solenoid pulse to verify liquid flow. Visual confirmation of test execution |
| FR-53 | Fault Scenario 1: Rover Stuck | Trigger: Motors at 410 RPM but position unchanged at 63.2m for > 5 seconds. Response: Auto-disengage motors to prevent VESC burnout or soil digging. Alert with row/meter location and manual recovery instructions |
| FR-54 | Fault Scenario 2: Critical Battery Trapped | Trigger: Battery < 15% and auto Return-to-Home fails due to path obstruction. Alert: "Battery at 12%, manual retrieval required from Row 4." Recovery instructions for battery swap or manual tow |
| FR-55 | Fault Scenario 3: Single Motor Failure | Trigger: One motor (RR) drawing 0.0A / 0 RPM with Hall Sensor fault while 3 motors operational. Alert: identifies dead motor, notes differential steering compensation. Technician instruction: inspect 3-phase wires and Hall sensor plug on VESC Controller #4 |
| FR-56 | Fault Scenario 4: Nozzle Clog via Camera | Trigger: Solenoid #2 activated (350ms) + pump engaged, but under-canopy camera detects no spray mist or leaf wetness change. Alert: identifies blocked nozzle. Technician instruction: clean mesh filter and run purge test |
| FR-57 | Fault Scenario 5: Thermal / Obstacle Watchdog | Trigger: Jetson Orin temperature > 78°C OR front ToF obstacle sensor timeout. Alert: clean ToF lens and verify Jetson cooling fan RPM |
| FR-58 | Fault toggle simulation | Each fault can be toggled active/inactive for demonstration purposes |

### 6.7 Screen 6 — AI Low-Confidence Active Learning Queue (`opt-ai-hil`)

| ID | Requirement | Details |
|----|-------------|---------|
| FR-60 | Low-confidence image queue | Display all leaf images where AI confidence was < 75% |
| FR-61 | Review card per item | Shows: Row + meter location, AI guess (bilingual), confidence %, environmental note (e.g., "Strong sunlight on leaf may mimic Spider Mite yellowing") |
| FR-62 | Confirm Diagnosis button | Marks item as confirmed; adds to verified training data pool |
| FR-63 | Correct Classification button | Allows reclassification (e.g., "Calcium Deficiency / Tip Burn"); corrects model drift in future retraining |

### 6.8 Export Compliance Certificate Modal

| ID | Requirement | Details |
|----|-------------|---------|
| FR-70 | Report modal | Triggered from Screen 3's PDF button. Title: "GlobalG.A.P Compliance Audit Log" |
| FR-71 | Report header data | Greenhouse ID: Strawberry GH #1 (12 rows). Scan date and rover mission log. Chemical reduction achieved: 78.6%. Total volume sprayed: 3.2 L. Residue status: "100% Zero-Residue Safe" |
| FR-72 | Spray audit table | Per-row breakdown: Row # → Sector location → Detected pathogen + confidence % → Volume dispensed (mL) |
| FR-73 | Report actions | Close modal. Download PDF certificate |

### 6.9 Sidebar Navigation & Information Architecture

| ID | Requirement | Details |
|----|-------------|---------|
| FR-80 | Core MVP section | 4 navigation buttons with green gradient active state: (1) Greenhouse & ROI Summary, (2) Rows Map & AI Snapshots, (3) Quick Control & Local Sync, (4) Yield Forecast & PHI Safety Lock |
| FR-81 | Hardware Diagnostics & Add-ons section | 2 navigation buttons with amber gradient active state: (5) Smart Hardware Fault Diagnostics (with live fault count badge), (6) Low-Confidence AI Review |
| FR-82 | Offline Architecture Explainer card | Sidebar information card explaining the 3-step offline-first workflow: (1) Local access point, (2) Compressed 90KB snapshots, (3) One-click cloud sync at office. Designed for investor comprehension |

---

## 7. Non-Functional Requirements

### 7.1 Performance

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-01 | Dashboard initial render time | < 2 seconds on mobile devices over local Wi-Fi |
| NFR-02 | Heatmap `onMouseMove` responsiveness | < 16ms per frame (60 FPS interactive tracking) |
| NFR-03 | ROI calculator reactivity | Instant slider response with no visible lag |
| NFR-04 | Sync progress simulation | Smooth 450ms interval updates |
| NFR-05 | Image compression | All leaf snapshots ≤ ~90 KB for local Wi-Fi transfer |

### 7.2 Usability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-10 | Bilingual completeness | 100% of UI text available in both Arabic and English |
| NFR-11 | RTL/LTR layout | Fully correct directional layout for both languages including icons, charts, and navigation |
| NFR-12 | Touch target minimum size | All field control buttons ≥ 44px × 44px (WCAG AA) |
| NFR-13 | Sunlight readability (Daylight Mode) | High contrast ratios meeting outdoor visibility standards |
| NFR-14 | Dark Mode contrast | Minimum 4.5:1 for body text, 3:1 for large text (WCAG AA) |
| NFR-15 | E-Stop discoverability | Always visible in top bar; red with high visual prominence; no scroll required |

### 7.3 Reliability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-20 | Offline operation | 100% of core features (Screens 1–4) functional without internet |
| NFR-21 | Data persistence | All local data survives browser refresh and rover power cycles |
| NFR-22 | PHI lock integrity | Auto-lock cannot be bypassed without explicit manual override action |
| NFR-23 | E-Stop hardware lock | E-Stop must cut both motor power and spray pump simultaneously |

### 7.4 Security

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-30 | Local network isolation | Dashboard accessible only within greenhouse local Wi-Fi network |
| NFR-31 | PHI override audit trail | All manual PHI lock/unlock actions must be logged with timestamp |
| NFR-32 | Export certificate integrity | Generated PDF reports must include tamper-evident metadata |

### 7.5 Scalability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-40 | Multi-greenhouse support | ROI calculator supports 1–20 greenhouses; data model extensible to multi-greenhouse fleet |
| NFR-41 | Row count flexibility | Data model supports variable row counts (current: 12) |
| NFR-42 | Disease class extensibility | AI model and UI correction dropdown extensible to new disease categories |

### 7.6 Maintainability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-50 | Component architecture | Modular React components; each screen is an independent module |
| NFR-51 | Styling system | Tailwind CSS utility classes; consistent design tokens for colors, spacing, and typography |
| NFR-52 | Icon system | Lucide React icon library for consistent, tree-shakeable iconography |
| NFR-53 | Font loading | Google Fonts (Cairo) dynamically loaded; graceful fallback to system sans-serif |

---

## 8. Prerequisites & Dependencies

### 8.1 Hardware Prerequisites

| Prerequisite | Details |
|--------------|---------|
| Assembled Faddan Rover | 4WD chassis with VESC motor controllers, LiFePO4 battery, 3-nozzle spray system, under-canopy camera, Jetson Orin Nano, ESP32 microcontroller, ToF obstacle sensor |
| 2.4 GHz Outdoor Access Point | Mounted at greenhouse entrance; must cover 100m length reliably |
| Charging & Refill Dock | Located at greenhouse entrance for autonomous return-to-home |

### 8.2 Software Prerequisites

| Prerequisite | Details |
|--------------|---------|
| YOLOv11-seg model weights | Trained on strawberry foliar disease dataset (Botrytis, Powdery Mildew, Spider Mites) with instance segmentation masks |
| Jetson Orin firmware | Edge inference runtime for YOLOv11-seg; image compression pipeline; CAN-Bus motor control interface |
| ESP32 firmware | UART/CAN heartbeat (100ms), sensor aggregation (battery, tank, ToF, Hall sensors), solenoid valve control |
| VESC firmware | 4× VESC motor controllers with CAN-Bus daisy chain; RPM and current reporting |

### 8.3 Frontend Dependencies

| Dependency | Purpose |
|------------|---------|
| React 18+ | UI framework |
| Tailwind CSS 3+ | Utility-first CSS framework |
| Lucide React | Icon library (53 icons used) |
| Google Fonts (Cairo) | Arabic-optimized font family |
| Vite (recommended) | Build tool and dev server |

### 8.4 Agronomic Prerequisites

| Prerequisite | Details |
|--------------|---------|
| Greenhouse setup | 12 rows × 100 meters, strawberry crop, raised bed or ground-level cultivation |
| Fungicide/Miticide | Compatible concentrated liquid formulation (dilution ratio for 9.2× dilution factored into ROI calculator) |
| Agronomist availability | At least one trained agronomist to review AI detections and provide feedback via Human-in-the-Loop queue |

---

## 9. Out of Scope

The following capabilities are explicitly **not included** in version 1.0:

| Item | Reason |
|------|--------|
| Multi-greenhouse fleet management | V1 targets single greenhouse (GH #1); fleet dashboard deferred to V2 |
| Real cloud backend / database | V1 uses local storage + mock data; production backend deferred |
| Actual PDF generation engine | V1 displays report modal with structured data; real PDF rendering deferred |
| User authentication / login system | V1 operates on trusted local network; auth deferred to cloud-connected V2 |
| Multi-crop support | V1 is strawberry-specific; tomato, pepper, and cucumber models deferred |
| Autonomous navigation / path planning | V1 follows fixed row-by-row sequential scan; dynamic replanning deferred |
| Drone / aerial integration | Rover-only system in V1 |
| Automated irrigation control | V1 provides climate alerts with manual action recommendations only |
| Fertigation dosing | V1 handles only pesticide/fungicide spray, not nutrient delivery |
| Historical trend analytics | V1 shows current-day snapshot only; time-series dashboards deferred to V2 |
| Mobile native app (iOS/Android) | V1 is a responsive web app; native app deferred |
| Multi-user role-based access | V1 is single-user; RBAC deferred to cloud-connected V2 |
| Integration with ERP/farm management software | V1 is standalone; API integrations deferred |
| Real-time WebSocket/MQTT communication | V1 uses simulated state; real protocol integration deferred to production build |
| Automated AI retraining pipeline | V1 collects labels via Human-in-the-Loop; actual retraining runs offline separately |

---

## 10. Assumptions

1. The greenhouse has a standard 12-row × 100-meter layout with uniform row spacing.
2. The 2.4 GHz access point provides reliable coverage across the full 100m greenhouse length.
3. The rover can physically navigate all 12 rows without manual intervention (barring obstacle faults).
4. The LiFePO4 battery provides sufficient charge to complete a full 12-row scan (~48 minutes) on a single charge under normal conditions.
5. The concentrated fungicide has a known dilution ratio and cost per liter that the agronomist can input into the ROI calculator.
6. The YOLOv11-seg model has been pre-trained and validated to detect Botrytis Gray Mold, Powdery Mildew, and Spider Mites with ≥ 90% mAP on the target strawberry cultivar.
7. The PHI compliance threshold of 72 hours (3 days) is accepted by the target export markets.
8. At least one agronomist reviews the Human-in-the-Loop queue within 24 hours of each mission.
9. Currency for all financial calculations is Egyptian Pound (EGP).
10. The dashboard is accessed via a modern browser (Chrome, Safari, Firefox) on a smartphone, tablet, or laptop connected to the local greenhouse Wi-Fi network.

---

## 11. Data Structures & Mock Data Reference

### 11.1 Pinpoint Detection Record

```
{
  id: string,            // e.g., 'pt-7'
  rowId: number,         // 1–12
  meter: number,         // 0.0–100.0
  status: enum,          // 'critical' | 'warning' | 'treated'
  diseaseAr: string,     // Arabic disease label
  diseaseEn: string,     // English disease label
  confidence: number,    // 0–100 (percentage)
  sprayed: boolean,      // whether micro-dose was applied
  ml: number,            // milliliters dispensed (0 if not sprayed)
  time: string,          // capture timestamp
  sizeKb: number,        // compressed image size
  verified: enum,        // 'pending' | 'approved' | 'corrected'
  correctedLabel: string // alternative label if corrected by agronomist
}
```
**Mock dataset:** 21 pinpoints across 12 rows.

### 11.2 Row Data Record

```
{
  id: number,                // 1–12
  nameAr: string,            // Arabic row name
  nameEn: string,            // English row name
  phiLocked: boolean,        // whether chemical spray is hardware-locked
  harvestDaysLeft: number,   // countdown to harvest
  ripeKg: number,            // ripe red strawberry weight (kg)
  turningKg: number,         // turning pink weight (kg)
  greenKg: number,           // green (unripe) weight (kg)
  traditionalLiters: number, // baseline blanket spray volume (always 1.25L per row)
  faddanLiters: number,      // actual Faddan spot-spray volume
  segments: {                // 3 segments per row (start/middle/end)
    start: SegmentData,
    middle: SegmentData,
    end: SegmentData
  }
}
```

### 11.3 Hardware Fault Record

```
{
  id: string,               // e.g., 'rover-stuck'
  active: boolean,          // currently active
  severity: enum,           // 'critical' | 'warning'
  titleAr: string,          // Arabic fault title
  titleEn: string,          // English fault title
  sensorLogicAr: string,    // Arabic sensor diagnosis explanation
  sensorLogicEn: string,    // English sensor diagnosis explanation
  alertMessageAr: string,   // Arabic alert message
  alertMessageEn: string,   // English alert message
  actionAr: string,         // Arabic technician recovery instruction
  actionEn: string          // English technician recovery instruction
}
```
**Mock dataset:** 5 fault scenarios.

### 11.4 Human-in-the-Loop Review Item

```
{
  id: string,            // e.g., 'hil-101'
  row: string,           // location description (bilingual)
  aiGuessAr: string,     // Arabic AI prediction
  aiGuessEn: string,     // English AI prediction
  confidence: number,    // 0–100 (< 75 triggers HIL routing)
  status: enum,          // 'pending' | 'confirmed' | 'corrected'
  noteAr: string         // environmental context note in Arabic
}
```
**Mock dataset:** 3 items (Row 2 @ 18.0m, Row 7 @ 19.5m, Row 11 @ 44.0m).

---

## 12. Glossary

| Term | Definition |
|------|-----------|
| **PHI (Pre-Harvest Interval)** | The minimum number of days that must elapse between the last pesticide application and crop harvest to ensure residue levels meet food safety standards |
| **GlobalG.A.P** | An international standard for Good Agricultural Practices; compliance is required for EU export of fresh produce |
| **Zero-Residue** | Produce with pesticide residue levels below the detection limit of standard analytical methods |
| **YOLOv11-seg** | A state-of-the-art real-time instance segmentation model; "seg" denotes pixel-level mask output per detection |
| **Spot Spraying** | Precision application of pesticide only to individually identified infected leaves, as opposed to blanket spraying |
| **Blanket Spraying** | Traditional method of uniformly spraying the entire greenhouse regardless of infection status |
| **VESC** | Open-source electronic speed controller for brushless DC motors; used to drive the rover's 4 independent wheels |
| **CAN-Bus** | Controller Area Network; a vehicle bus standard for real-time communication between microcontrollers and motor controllers |
| **Hall Sensor** | Magnetic sensor in brushless motors that provides rotor position feedback for commutation and RPM measurement |
| **ToF (Time-of-Flight) Sensor** | Distance sensor using infrared light pulses; used for front obstacle detection on the rover |
| **Jetson Orin Nano** | NVIDIA edge AI computing module; runs the YOLOv11-seg model for on-device inference |
| **ESP32** | Low-power microcontroller handling sensor aggregation, heartbeat monitoring, and solenoid valve control |
| **LiFePO4** | Lithium Iron Phosphate battery chemistry; chosen for safety, long cycle life, and stable discharge in hot greenhouse environments |
| **Active Learning** | Machine learning paradigm where the model identifies its own uncertain predictions and routes them to a human expert for labeling |
| **HIL (Human-in-the-Loop)** | System design pattern where low-confidence AI predictions are queued for expert review before being accepted as training data |
| **EGP** | Egyptian Pound (currency) |
| **RTL** | Right-to-Left text direction (Arabic) |
| **LTR** | Left-to-Right text direction (English) |
| **Micro-Climate Heatmap** | Spatial visualization of temperature or humidity distribution across the greenhouse floor plan |

---

*End of PRD — Faddan AI Robotics v1.0*
