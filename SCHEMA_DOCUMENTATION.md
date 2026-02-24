# PayMedia CRM - Phase 1 Schema Documentation

## Overview
This document provides detailed documentation for the Phase 1 database schema of the PayMedia Enterprise CRM & Project Lifecycle System.

## Core Principles

### 1. No-Delete Architecture
All tables include `is_deleted` boolean and `archived_at` timestamp fields. Records are NEVER hard-deleted - only soft-deleted/archived.

### 2. Version Control
Entities that require versioning (Proposals, Agreements, POs) have:
- `version_number` - String (e.g., "1.0", "1.1", "2.0")
- `is_current_version` - Boolean
- `parent_version_id` - Self-referencing FK to track version history

### 3. Audit Trail
Every table includes:
- `created_by` - FK to User
- `modified_by` - FK to User
- `created_at` - Timestamp
- `updated_at` - Auto-updated timestamp
- `version` - Integer for optimistic locking (where applicable)

### 4. Immutable Audit Logs
All system actions are logged in the `audit_logs` table with:
- User ID, IP address, timestamp
- Action type (CREATE, UPDATE, DELETE, VIEW, etc.)
- Old and new values (JSON)
- Request metadata

## Module Documentation

---

## MODULE 01: Organisation & Contact Management

### Organisation Table
**Purpose:** Store company profiles with hierarchical relationships

**Key Fields:**
- `name` - Display name
- `legal_name` - Official registered name
- `registration_number` - Unique business registration
- `health_score` - 0-100 score for client health
- `parent_org_id` - Self-referencing for org hierarchy

**Statuses:** ACTIVE, INACTIVE, PROSPECT, ARCHIVED

**Business Rules:**
- Can have multiple child organisations
- Health score calculated based on various factors
- Address fields for geographic tracking

### Contact Table
**Purpose:** Individual contact persons within organisations

**Key Fields:**
- `organisation_id` - FK to Organisation (required)
- `full_name` - Computed from first_name + last_name
- `role_type` - Enum: CEO, CTO, CFO, etc.
- `is_primary_contact` - Boolean
- `is_decision_maker` - Boolean

**Business Rules:**
- Unique email per organisation (composite unique index)
- One contact can be marked as primary per org
- Preferred communication method tracking

---

## MODULE 02: Lead Management

### Lead Table
**Purpose:** Track potential opportunities with scoring and qualification

**Key Fields:**
- `lead_score` - 0-100 automated scoring
- `source` - Enum: WEBSITE, REFERRAL, COLD_CALL, etc.
- `status` - NEW, CONTACTED, QUALIFIED, CONVERTED, DISQUALIFIED
- `stage` - QUALIFICATION, NEEDS_ANALYSIS, PROPOSAL_SENT, NEGOTIATION
- `assigned_to` - FK to User (round-robin assignment)
- `converted_to_opportunity_id` - One-to-one FK to Opportunity

**Business Rules:**
- Round-robin assignment to sales team
- Lead scoring based on engagement, company size, etc.
- Cannot be deleted after conversion
- Disqualification requires reason

**Conversion Flow:**
```
Lead (QUALIFIED) → Convert → Opportunity (created) → Lead (converted_to_opportunity_id set)
```

---

## MODULE 03: Opportunity Management

### Opportunity Table
**Purpose:** Sales pipeline management with financial tracking

**Key Fields:**
- `converted_from_lead_id` - One-to-one FK to Lead (if converted)
- `estimated_value` - Decimal(15,2)
- `probability` - 0-100 percentage
- `expected_revenue` - calculated: estimated_value × probability
- `stage` - DISCOVERY, QUALIFICATION, PROPOSAL, NEGOTIATION, CLOSED_WON, CLOSED_LOST
- `expected_close_date` - Forecast
- `actual_close_date` - When deal closes

**Business Rules:**
- Linked to Organisation and optionally Contact
- Can have multiple Proposals
- Win/loss tracking with reasons
- Competitor intelligence tracking

**Pipeline Stages:**
1. DISCOVERY - Initial contact
2. QUALIFICATION - Needs assessment
3. PROPOSAL - Proposal sent
4. NEGOTIATION - Terms discussion
5. CLOSED_WON - Deal won
6. CLOSED_LOST - Deal lost

---

## MODULE 04: Proposal Management

### Proposal Table
**Purpose:** Version-controlled proposals with approval workflow

**Key Fields:**
- `proposal_number` - Unique: PROP-2026-001 (auto-generated)
- `opportunity_id` - FK to Opportunity (required)
- `version_number` - "1.0", "1.1", "2.0"
- `is_current_version` - Boolean
- `parent_version_id` - Self-reference for version chain
- `proposal_value` - Decimal(15,2)
- `valid_until` - Expiry date

**Statuses:** DRAFT, PENDING_APPROVAL, APPROVED, REJECTED, SENT_TO_CLIENT, ACCEPTED, DECLINED, EXPIRED

**Version Control Logic:**
```
v1.0 (current) → Edit → v1.1 created (current=true) → v1.0 (current=false)
```

**Approval Workflow:**
1. DRAFT - Created by sales
2. PENDING_APPROVAL - Submitted for review
3. APPROVED - Management approved
4. SENT_TO_CLIENT - Delivered to client
5. ACCEPTED - Client accepted
6. DECLINED - Client rejected
7. EXPIRED - Past valid_until date

**Business Rules:**
- Only current version can be edited (creates new version)
- Approved proposals cannot be deleted
- Client view tracking (viewed_by_client_at)
- Document storage in cloud (document_url)

---

## MODULE 05: Purchase Order Management

### PurchaseOrder Table
**Purpose:** Track client POs with utilization monitoring

**Key Fields:**
- `po_number` - Client's PO number (unique)
- `agreement_id` - FK to Agreement (optional)
- `po_value` - Total PO amount
- `utilized_amount` - Amount consumed
- `remaining_amount` - Calculated: po_value - utilized_amount
- `utilization_percentage` - 0-100%
- `amendment_count` - Number of amendments
- `original_po_id` - Self-reference for amendments

**Statuses:** ACTIVE, FULLY_UTILIZED, EXPIRED, CANCELLED, ARCHIVED

**Amendment Logic:**
```
Original PO (po_value: 100k) → Amendment (po_value: 150k, original_po_id: original.id)
```

**Business Rules:**
- PO utilization tracked against project expenses
- Alerts when utilization > 80%
- Amendments create new PO record linked to original
- Cannot delete active POs with utilization

---

## MODULE 06: Agreement & Contract Management

### Agreement Table
**Purpose:** Store contracts (MSA, SOW, NDA) with signature tracking

**Key Fields:**
- `agreement_number` - AGR-2026-001 (auto-generated)
- `type` - MSA, SOW, NDA, SLA, AMENDMENT, OTHER
- `version_number` - Version control
- `contract_value` - Optional (for SOW)
- `start_date`, `end_date`, `expires_at`
- `auto_renewal` - Boolean
- `renewal_notice_days` - Alert before expiry
- `signed_by_us`, `signed_by_client` - Boolean flags
- `clauses` - JSON: Clause library

**Statuses:** DRAFT, PENDING_SIGNATURE, SIGNED, ACTIVE, EXPIRED, TERMINATED, ARCHIVED

**Digital Signature Tracking:**
- Internal signatory name & date
- Client signatory name & date
- Document URL for signed copy

**Renewal Management:**
- `renewal_status` - NOT_DUE, APPROACHING, DUE, RENEWED, NOT_RENEWED
- Automated alerts based on `renewal_notice_days`

**Business Rules:**
- MSA (Master Service Agreement) can have multiple SOWs
- Each Agreement can link to multiple POs
- Expiry alerts trigger 30, 60, 90 days before expiration
- Version control for amendments

---

## MODULE 13: Communication Logging

### Communication Table
**Purpose:** Log all interactions (email, calls, meetings) with clients

**Key Fields:**
- `type` - EMAIL, PHONE_CALL, VIDEO_CALL, MEETING, SMS, etc.
- `direction` - INBOUND, OUTBOUND
- `organisation_id`, `contact_id` - Linkage (optional)
- `lead_id`, `opportunity_id` - Pipeline linkage (optional)
- `subject`, `body` - Content
- `meeting_date`, `meeting_attendees` - For meetings
- `call_duration`, `call_recording_url` - For calls
- `attachment_urls` - JSON array of files

**Statuses:** SCHEDULED, COMPLETED, CANCELLED, NO_RESPONSE

**Business Rules:**
- Can link to multiple entity types (polymorphic)
- Meeting notes stored as rich text
- Call recordings stored securely
- Search and filter by date, type, entity

---

## AUDIT & SECURITY

### AuditLog Table
**Purpose:** Immutable audit trail for compliance

**Key Fields:**
- `user_id` - Who performed the action (nullable for system)
- `action` - LOGIN, LOGOUT, CREATE, UPDATE, DELETE, VIEW, DOWNLOAD, etc.
- `entity_type`, `entity_id` - What was affected
- `ip_address`, `user_agent` - Request details
- `old_values`, `new_values` - JSON of changes
- `timestamp` - When (immutable)

**Business Rules:**
- **NEVER** deleted (true immutability)
- 7-year retention as per compliance requirements
- Used for security audits and compliance reports
- Indexed for fast querying

---

## Relationships Overview

```
Organisation (1) → (N) Contact
Organisation (1) → (N) Lead
Organisation (1) → (N) Opportunity
Organisation (1) → (N) Agreement

Contact (1) → (N) Lead
Contact (1) → (N) Opportunity

Lead (1) → (1) Opportunity (conversion)

Opportunity (1) → (N) Proposal
Opportunity (1) → (N) Agreement

Agreement (1) → (N) PurchaseOrder

User → Created/Modified all entities
User → Assigned to Lead/Opportunity
User → Audit Logs
```

## Indexes & Performance

All tables have indexes on:
- Primary Keys (automatic)
- Foreign Keys (automatic)
- `is_deleted` - Fast filtering of active records
- `status` - Common query filter
- `created_at` - Time-based queries
- Entity-specific fields (email, numbers, etc.)

## Data Integrity Rules

1. **Cascade Deletes:** Disabled - use soft deletes
2. **Foreign Key Constraints:** Enforced at DB level
3. **Unique Constraints:** On business keys (emails, numbers)
4. **Check Constraints:** For enums and valid ranges
5. **Not Null:** Required fields enforced

## Migration Strategy

```bash
# Initial migration
npm run prisma:migrate

# After schema changes
npx prisma format  # Format schema
npx prisma validate  # Validate syntax
npm run prisma:migrate  # Create migration
```

## Security Considerations

1. **Sensitive Data:** Password hashes only, no plain text
2. **PII:** Contact information encrypted at rest (future)
3. **Audit Trail:** All changes logged
4. **Access Control:** RBAC enforced at API layer
5. **Soft Deletes:** Data retention for compliance

---

## Next Phase: Phase 2 (Project Delivery Modules)

Will add:
- Client Onboarding
- Project Lifecycle Documentation
- Resource Management
- Risk Register (RAID)
- Payment & Financial Tracking
- Renegotiation & Change Management

---

**Document Version:** 1.0  
**Last Updated:** 2026-02-24  
**Author:** PayMedia Development Team
