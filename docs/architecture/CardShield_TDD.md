# CardShield Technical Design Document (TDD)

## 1. Overview

### Project Name

CardShield

### Project Type

Near Real-Time Credit Card Fraud Detection and Investigation Platform

### Cloud Platform

Google Cloud Platform (GCP)

### Primary Technologies

* Google Cloud Storage (GCS)
* Pub/Sub
* Apache Beam
* Dataflow
* BigQuery
* Secret Manager
* Cloud Functions
* Streamlit
* ServiceNow REST API

---

## 2. Problem Statement

Financial institutions process millions of card authorization transactions daily.

Challenges include:

* Detecting fraudulent activity quickly
* Maintaining PCI compliance
* Preserving complete transaction history
* Handling malformed transactions
* Supporting fraud investigation workflows
* Integrating with enterprise incident management systems

CardShield addresses these challenges using a cloud-native, event-driven architecture.

---

## 3. Functional Requirements

### FR-001

Ingest credit card transaction data from streaming and batch sources.

### FR-002

Validate transaction data without interrupting transaction processing.

### FR-003

Route invalid transactions to a Dead Letter Queue (DLQ).

### FR-004

Tokenize PCI-sensitive data before storage.

### FR-005

Store processed transactions in BigQuery.

### FR-006

Apply fraud detection rules.

### FR-007

Generate fraud cases for suspicious transactions.

### FR-008

Send fraud alerts through email.

### FR-009

Create ServiceNow incidents automatically.

### FR-010

Provide analysts access to 180 days of transaction history.

### FR-011

Provide live monitoring of customer activity while investigations remain open.

### FR-012

Support fraud investigation workflow updates.

### FR-013

Implement row-level and column-level security.

### FR-014

Support automated reprocessing of recoverable rejected transactions.

---

## 4. Non-Functional Requirements

### Availability

99.9%

### Scalability

Support:

* 50 million transactions/day
* Peak bursts of 10,000 transactions/second

### Latency

Fraud detection latency target:

< 60 seconds

### Security

PCI-sensitive data must never be stored in analytical tables.

### Auditability

All fraud decisions must be explainable and traceable.

---

## 5. Architecture

### Logical Architecture

Transaction Feed

→ Pub/Sub

→ Dataflow

→ Validation

→ Tokenization

→ Fraud Detection

→ BigQuery

→ Alerts

→ Investigation Console

---

## 6. Data Architecture

### Bronze Layer

Purpose:

Raw immutable landing zone.

Dataset:

cardshield_bronze

Tables:

* raw_transactions
* transaction_rejects
* tokenization_audit
* raw_file_audit

Retention:

7 Days

---

### Silver Layer

Purpose:

Validated and tokenized transaction data.

Dataset:

cardshield_silver

Tables:

* transactions
* merchant_profile
* customer_profile
* reprocess_audit

Retention:

5 Years

---

### Gold Layer

Purpose:

Fraud analytics and investigations.

Dataset:

cardshield_gold

Tables:

* fraud_cases
* fraud_rule_audit
* fraud_incidents
* fraud_investigations
* case_snapshots
* customer_behavior_profile
* customer_transaction_timeline
* case_activity_monitor
* case_sla
* dq_metrics

Retention:

7 Years

---

## 7. Transaction Processing Flow

### Step 1

Transaction enters Pub/Sub.

### Step 2

Dataflow validates record.

Validation categories:

* Technical
* Business
* PCI

### Step 3

Invalid transactions routed to:

transaction_rejects

### Step 4

Valid transactions proceed to tokenization.

### Step 5

PAN removed.

Token generated using:

Salted SHA-256

### Step 6

Fraud features generated.

### Step 7

Fraud rules evaluated.

### Step 8

Fraud cases created.

### Step 9

Alerts generated.

---

## 8. Tokenization Design

### Algorithm

SHA-256

### Salt

Stored in:

Google Secret Manager

### Formula

TOKEN = SHA256(SALT + PAN)

### Additional Attributes

Stored:

* card_last_4
* card_bin
* card_network

Removed:

* card_number

---

## 9. Data Quality Framework

### Technical Validations

* Missing transaction ID
* Invalid timestamp
* Invalid JSON

### Business Validations

* Negative amount
* Invalid country
* Invalid merchant

### PCI Validations

* Invalid PAN length
* Failed Luhn validation

### Output

ValidationResult

Containing:

* is_valid
* error_codes
* reject_category
* reprocessable_flag

---

## 10. Dead Letter Queue Framework

### Reject Table

transaction_rejects

### Reject Categories

* TECHNICAL
* BUSINESS
* PCI
* SECURITY
* SYSTEM

### Reprocessing States

* NEW
* RETRY_PENDING
* REPROCESSED_SUCCESS
* REPROCESS_FAILED
* MANUAL_REVIEW

---

## 11. Reprocessing Framework

### Execution Schedule

Daily

11 PM CST

### Auto-Fix Examples

* Timestamp normalization
* Country standardization
* Currency standardization
* Amount formatting

### Audit Table

reprocess_audit

---

## 12. Fraud Detection Framework

### Rule F001

High Dollar Transaction

### Rule F002

Foreign Transaction

### Rule F003

Merchant Risk

### Rule F004

Velocity

### Rule F005

Card Testing

### Rule F006

Impossible Travel

### Rule F007

Spend Deviation

---

## 13. Fraud Risk Scoring

Risk score calculated using cumulative rule scores.

### Severity Mapping

Critical:

90+

High:

75-89

Medium:

50-74

Low:

0-49

---

## 14. Customer Behavior Profiling

Refresh Frequency:

Daily

### Metrics

* avg_spend_30d
* avg_spend_90d
* avg_spend_180d
* max_spend_180d
* top_merchant
* top_city
* top_country
* fraud_case_count

---

## 15. Fraud Case Management

### Case States

* NEW
* ASSIGNED
* IN_PROGRESS
* PENDING_CUSTOMER
* PENDING_MERCHANT
* CONFIRMED_FRAUD
* FALSE_POSITIVE
* CLOSED

### Case Generation Threshold

Risk Score >= 70

---

## 16. Alerting Framework

### Email Alerts

Generated for:

Severity >= HIGH

### ServiceNow Incidents

Generated for:

Severity >= HIGH

### Assignment Groups

* Fraud_Tier_1
* Fraud_Tier_2
* Fraud_Tier_3

---

## 17. Investigation Workspace

Provides:

* Fraud case summary
* Suspicious transaction details
* 180-day history
* Customer profile
* Live transaction monitoring
* Investigation notes
* Incident linkage

---

## 18. ServiceNow Integration

### Incident Lifecycle

Create

Update

Resolve

Close

### Incident Tracking Table

fraud_incidents

---

## 19. SLA Framework

### Critical

15 Minutes

### High

1 Hour

### Medium

4 Hours

### Low

24 Hours

Tracked via:

case_sla

---

## 20. Security Model

### Column-Level Security

Protected Fields:

* customer_email
* customer_phone
* device_id
* ip_address_hash

### Row-Level Security

Access controlled by:

* analyst region
* assignment group

---

## 21. Monitoring

Operational Metrics:

* Transactions Processed
* Fraud Cases Generated
* Fraud Detection Latency
* DLQ Volume
* Reprocessing Success Rate
* SLA Breaches

---

## 22. Disaster Recovery

Pub/Sub retention protects transaction ingestion.

DLQ ensures transaction preservation.

Reprocessing framework enables recovery.

Reconciliation ensures transaction completeness.

---

## 23. Future Enhancements

### Phase 2

* Machine Learning Models
* Graph Fraud Detection
* Real-Time Feature Store
* GenAI Investigation Assistant

---

## 24. Success Criteria

CardShield is considered successful when:

* No transaction loss occurs
* Fraud cases generated within latency targets
* PCI-sensitive data is not stored in analytical layers
* Analysts can investigate using historical and live customer activity
* Fraud alerts and incidents are automatically generated
* Governance controls are enforced
