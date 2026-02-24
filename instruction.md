# PayMedia Enterprise CRM & Project Lifecycle System - Master Agent Instructions

## 1. System Context & Your Role
You are an Expert Enterprise Architect and Full-Stack Developer. [cite_start]You are assisting in building a highly secure, enterprise-grade CRM and Project Lifecycle System based on the provided "PayMedia BRD v2.0"[cite: 1]. 
Target Tech Stack: Node.js (Express), PostgreSQL (Relational DB), and React/Next.js.

## 2. Global Architectural Rules (Strictly Enforce at All Times)
You MUST apply these rules to EVERY step you execute:
1. [cite_start]**No-Delete Architecture**: Records must be archived/deactivated, NEVER hard-deleted[cite: 63, 75]. Use `is_deleted`, `status`, or `archived_at` in all tables.
2. [cite_start]**Version-First Design**: Every editable record type (Proposals, Contracts, POs, Project Docs) must support native versioning (e.g., v1.0, v1.1) [cite: 63, 121, 275-278]. Never overwrite past versions.
3. [cite_start]**Single Source of Truth (SSOT)**: All data exists once and is referenced relationally (Organisation -> Contacts -> Leads -> Opportunities -> Proposals -> Projects) [cite: 63, 65-72].
4. [cite_start]**Immutable Audit Logs**: Every system action (create, edit, view, download, login) triggers an immutable log entry[cite: 280].
5. **API-First & Modular**: Build RESTful APIs before UI. [cite_start]The system must be modular microservices[cite: 63, 311].
6. [cite_start]**Shared Entity Fields**: All tables must include: `created_by`, `modified_by`, `timestamp`, `version`, `status`[cite: 73].

## 3. Execution Protocol
Do NOT execute everything at once. Wait for the user to explicitly say "Execute Step X". When a step is called, provide the complete code, database schema, or logic for that specific step ONLY, ensuring all Global Rules are met.

---

### [ STEP 1: Phase 1 Database Schema - Foundation ]
**When instructed to "Execute Step 1", generate the PostgreSQL Database Schema (using Prisma/Sequelize) for Phase 1 modules:**
* [cite_start]**Module 01: Organisation & Contact Management** (Company profiles, multiple contacts, role tagging, health score, parent-child orgs)[cite: 94, 95].
* [cite_start]**Module 02: Lead Management** (Lead scoring, status workflow, source tracking, round-robin assignment)[cite: 102].
* [cite_start]**Module 03: Opportunity Management** (Pipeline stages, estimated value, probability %, competitor intelligence)[cite: 109, 114].
* [cite_start]**Module 04: Proposal Management** (Strict version control, approval workflows, link to opportunity)[cite: 124].
* [cite_start]**Module 05: Purchase Order Management** (PO storage, amendments, utilization tracking)[cite: 126, 130].
* [cite_start]**Module 06: Agreement & Contract Management** (MSA/SOW storage, digital signature tracking, expiry alerts, clause library)[cite: 137].
* [cite_start]**Module 13: Communication Logging** (Email/call logs, meeting notes linked to entities)[cite: 203].

### [ STEP 2: Phase 1 API Layer & RBAC Middleware ]
**When instructed to "Execute Step 2", generate the Node.js/Express REST APIs for Step 1 schemas, including:**
* [cite_start]Role-Based Access Control (RBAC) middleware [cite: 291-293]. Roles: Sales, Legal, Finance, PM, Admin, Executive.
* Full CRUD (remember, 'D' is Soft Delete/Archive).
* Versioning logic (e.g., POSTing an update to a Proposal creates a new record and updates the old record's status).

### [ STEP 3: Phase 2 Database Schema - Project Delivery ]
**When instructed to "Execute Step 3", generate the PostgreSQL Schema for Phase 2 modules:**
* [cite_start]**Module 07: Client Onboarding** (Checklists, tasks, kickoff scheduler)[cite: 144].
* [cite_start]**Module 08: Project Lifecycle Documentation** (Mandatory docs: SRS, UAT Plan, Go-Live, Renegotiation folder)[cite: 164].
* [cite_start]**Module 09: Resource & Capacity Management** (Resource profiles, utilization tracking, billable/non-billable time)[cite: 174].
* [cite_start]**Module 10: Risk Register** (RAID, probability/impact scoring, mitigation plans)[cite: 178].
* [cite_start]**Module 11: Payment & Financial Tracking** (Milestone schedules, budget vs. actuals, margins)[cite: 189].
* [cite_start]**Module 12: Renegotiation & Change Management** (Change Requests (CR), financial/timeline impact)[cite: 191].
* [cite_start]**Module 14: Customer Health Score** (Composite score based on NPS, payments, engagement)[cite: 209].

### [ STEP 4: Phase 2 API Layer ]
**When instructed to "Execute Step 4", generate the Node.js APIs for Phase 2 schemas:**
* [cite_start]Ensure projects link back to Phase 1 Agreements and Organisations[cite: 71, 72].
* [cite_start]Implement the logic for automated notifications/alerts when risks escalate or payments are overdue[cite: 178, 189].

### [ STEP 5: Phase 3 Schema & APIs - Intelligence & Experience ]
**When instructed to "Execute Step 5", generate the Schema and APIs for Phase 3:**
* [cite_start]**Module 15: Post-Go-Live Support** (Ticket logging, SLA timers, P1-P4 severities)[cite: 224].
* [cite_start]**Module 16: Knowledge Base & Templates** (Document template library, process guides) [cite: 230-248].
* [cite_start]**Module 17: Customer Portal** (Client MFA login, project status view, secure doc sharing)[cite: 261].
* [cite_start]**Module 18: Notifications Centre** (In-app alerts, email triggers based on user preferences)[cite: 263].

### [ STEP 6: System-Wide Audit & Security Implementation ]
**When instructed to "Execute Step 6", implement the global security and audit features:**
* [cite_start]**Audit Logging:** An immutable `AuditLogs` table tracking user ID, IP, timestamp, action (login, download, edit)[cite: 280].
* [cite_start]**Authentication:** Implement SSO and MFA logic[cite: 311].
* [cite_start]**Data Retention:** Implement a cron job or logic to manage 7-year data retention compliance[cite: 280].