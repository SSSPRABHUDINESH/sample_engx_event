Here is the complete step-by-step workflow for every user role within the Smart Hospital Operations Platform based on your GCP HLD architecture.

---

### 1. Patient / Caregiver Journey

```
[Patient Mobile App] ──► [Firebase Auth] ──► [API Gateway] ──► [Patient & Triage Svc]
                                                                        │
[Push Notification] ◄── [Pub/Sub & FCM] ◄── [Cloud Spanner DB] ◄────────┘

```

* **Authentication:** The patient logs into the Patient Mobile Portal via OTP or Social Login using **Firebase Authentication**, receiving an authenticated JWT bearer token.
* **Intake & Appointment Scheduling:** The patient submits profile details, schedules an appointment, and inputs initial symptom/urgency details.
* **Data Ingestion:** The request passes through **Google Cloud API Gateway** to the **Patient & Triage Service (Cloud Run)**, which stores structured intake notes in **Cloud Firestore**.
* **Real-Time Visibility & Notifications:** When routing or queue statuses change, the **Notification Service** consumes domain events from **Cloud Pub/Sub** and pushes real-time queue updates, delay alerts, and test readiness notifications directly to the patient's device via **Firebase Cloud Messaging (FCM)**.

---

### 2. Triage Nurse / Receptionist Journey

```
[Nurse Dashboard] ──► [Cloud Identity / SSO] ──► [Triage Svc] <──► [Vertex AI Inference]
                                                        │                   │
[Patient Assigned] ◄── [Human Interceptor (Approve)] ◄──┴────────────── [XAI Engine]

```

* **Authentication:** The nurse authenticates using **Google Cloud Identity (Enterprise SSO)** with Multi-Factor Authentication (MFA).
* **Patient Intake Verification:** When a patient arrives (walk-in, scheduled, or emergency EMT), the nurse logs initial vitals (Heart Rate, $\text{SpO}_2$, BP) and symptoms into the Triage Dashboard.
* **AI-Assisted Scoring:** The **Patient & Triage Service** fires a low-latency gRPC request to **Vertex AI Prediction Endpoints**. Vertex AI calculates a dynamic risk/priority score, while **Vertex Explainable AI (XAI)** outputs the exact clinical rationale (e.g., *"Elevated priority due to SpO2 < 90%"*).
* **Human-in-the-Loop Validation:** The recommendation appears on the nurse's screen via the **Clinical Safety Interceptor**. The nurse reviews the AI score and either accepts or overrides the decision before routing the patient to the appropriate care unit.

---

### 3. Attending Physician / Specialist Journey

```
[Doctor Terminal] ──► [EHR / LIS Integration] ──► [Surgical & OT Service]
                                                          │
[Operation Scheduled] ◄── [Pub/Sub Event] ◄────── [Vertex AI Prediction]

```

* **Clinical Care & Diagnostics:** The physician accesses the patient’s live profile, pulling lab/radiology results integrated via **Cloud Healthcare API (HL7/DICOM)**.
* **Resource & OT Requests:** If emergency surgery or specialized care is needed, the doctor requests an Operating Theatre (OT) or ICU bed through the **Surgical & OT Service**.
* **Emergency Slot Allocation:** **Vertex AI** checks live OT availability and predicts emergency slot adjustments. The doctor confirms the allocated time slot, publishing an event to **Cloud Pub/Sub** to immediately prepare the surgical team and notify transport.

---

### 4. Operations & Bed Manager Journey

```
[Ops Dashboard] ──► [Bed Management Svc] <──► [Vertex AI (Demand Forecast)]
                           │                               │
[Bed Allocated] ◄── [Approve/Override] ◄────────────── [XAI Rationale]

```

* **Capacity Monitoring:** The Operations Manager monitors real-time bed utilization, ward capacities, and dynamic queue states backed by **Cloud Spanner** and **MemoryStore for Redis**.
* **Predictive Surge Planning:** **Vertex AI Pipelines** continuously stream arrival forecasts, no-show predictions, and capacity bottleneck warnings to the manager's dashboard 6–12 hours in advance.
* **AI Bed Allocation & Preemption:** When a high-risk emergency patient arrives and beds are full, Vertex AI recommends a preemption plan (e.g., *"Transfer routine Ward Bed 201 patient to discharge lounge to free bed for emergency admission"*).
* **Execution:** The Bed Manager clicks **Approve** on the dashboard. The **Bed Management Service** updates the database state, triggers a `BedAllocatedEvent` via **Cloud Pub/Sub**, and automatically issues dispatch orders to sanitation and orderly teams.

---

### 5. Clinical Safety Officer / Compliance Auditor Journey

```
[Audit Terminal] ──► [Audit & Compliance Svc] ──► [Cloud Bigtable / XAI Logs]

```

* **Immutable Decision Tracking:** Every AI-generated priority score, feature attribution (SHAP values), and clinical override is streamed through **Cloud Pub/Sub** into **Cloud Bigtable**.
* **Audit Trail Review:** The Safety Officer queries the **Audit & Compliance Service** to inspect historical decision logs, verifying that clinical overrides were justified and that AI risk models operated within regulatory compliance guidelines.
