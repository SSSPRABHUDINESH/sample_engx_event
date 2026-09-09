**Use Case 1: Emergency Patient Arrival & AI-Assisted Triage**

```
+-------------------------------------------------------------------------------+
| 1. PATIENT CHECK-IN & ENTRY                                                  |
| Triage Nurse inputs patient vitals (SpO2, HR, BP) & symptoms into dashboard.  |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 2. EDGE SECURITY & ROUTING                                                   |
| API Gateway verifies nurse security token -> Routes to Triage Microservice.   |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 3. AI RISK SCORING & EXPLANATION                                              |
| Real-Time Inference Engine calculates high risk score (<200 ms).              |
| XAI Engine attaches rationale: "Oxygen saturation < 90% and acute chest pain."|
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 4. HUMAN APPROVAL (SAFETY GUARDRAIL)                                         |
| Nurse reviews recommendation on Clinical Interceptor screen -> Clicks APPROVE.|
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 5. EVENT & NOTIFICATION                                                       |
| DB queue state updated -> IntakeEvent published to Event Bus -> Notification  |
| Service alerts cardiologist & trauma team on mobile devices.                   |
+-------------------------------------------------------------------------------+

```

---

**Use Case 2: Real-Time Bed Allocation & Emergency Preemption**

```
+-------------------------------------------------------------------------------+
| 1. BED REQUEST                                                                |
| Emergency doctor requests an ICU bed via Bed Management Microservice.          |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 2. AI BED OPTIMIZATION                                                        |
| Inference Engine checks DB/Cache -> Detects full ICU -> Generates plan:       |
| "Transfer Ward 3 Patient to Step-Down Unit 1B to free ICU Bed 102."           |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 3. HUMAN APPROVAL                                                             |
| Operations & Bed Manager views transfer plan + rationale -> Clicks APPROVE.   |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 4. EXECUTION & AUTOMATED DISPATCH                                             |
| - ICU Bed 102 state updated to RESERVED in DB.                               |
| - BedAllocatedEvent emitted to Event Bus.                                     |
| - Work orders sent to housekeeping & orderlies to prep room & transfer patient|
+-------------------------------------------------------------------------------+

```

---

**Use Case 3: Doctor Availability & OPD-to-Surgical OT Escalation**

```
+-------------------------------------------------------------------------------+
| 1. ROSTER VERIFICATION                                                        |
| OPD Doctor opens workstation -> Staff & Scheduling Service confirms on-call    |
| General Surgeon and Anesthetist availability.                                 |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 2. OT SCHEDULING REQUEST                                                      |
| Doctor submits urgent surgery order via Surgical & OT Microservice.            |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 3. AI SCHEDULING ENGINE                                                       |
| Inference Engine evaluates OT readiness, equipment, & surgeon schedules ->    |
| Reserves OT Room 4 for next open slot.                                        |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 4. SURGEON CONFIRMATION                                                       |
| Attending surgeon reviews proposed slot -> Approves order on terminal.        |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 5. EVENT BROADCASTING & SYNCHRONIZATION                                       |
| - SurgicalScheduledEvent broadcasted on Event Bus.                             |
| - Patient mobile app updated with pre-op instructions.                         |
| - Pre-surgical lab requests sent to Legacy EHR/LIS via HL7/DICOM.             |
+-------------------------------------------------------------------------------+

```

---

**Use Case 4: Predictive Operations & Surge Forecasting**

```
+-------------------------------------------------------------------------------+
| 1. BACKGROUND ANALYTICS                                                       |
| Continuous Model Retraining Pipeline analyzes time-series telemetry, historical|
| intake patterns, and seasonal/weather data from Time-Series Store.            |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 2. SURGE ALERT GENERATION                                                     |
| Inference Engine forecasts a 35% spike in Emergency arrivals for night shift. |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 3. OPERATIONAL ADJUSTMENT                                                     |
| Automated capacity warning sent directly to Operations Manager dashboard.     |
+-------------------------------------------------------------------------------+
                                       │
                                       ▼
+-------------------------------------------------------------------------------+
| 4. ACTION TAKEN                                                               |
| Manager calls in extra on-call nurses & pre-allocates ward beds via Staff &   |
| Scheduling Service ahead of surge.                                            |
+-------------------------------------------------------------------------------+

```
