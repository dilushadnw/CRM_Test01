# PayMedia Enterprise CRM & Project Lifecycle System

Enterprise-grade CRM and Project Management system built with Node.js, Express, PostgreSQL (Prisma), and React/Next.js.

## 🏗️ Architecture Overview

This system follows a **microservices architecture** with:
- **No-Delete Policy**: All records are archived, never hard-deleted
- **Version-First Design**: Native versioning for all editable documents
- **Single Source of Truth**: Relational data integrity
- **Immutable Audit Logs**: Complete tracking of all system actions
- **RBAC**: Role-Based Access Control for all modules

## 📦 Phase 1 Modules (Current)

✅ **Module 01**: Organisation & Contact Management  
✅ **Module 02**: Lead Management  
✅ **Module 03**: Opportunity Management  
✅ **Module 04**: Proposal Management (with strict version control)  
✅ **Module 05**: Purchase Order Management  
✅ **Module 06**: Agreement & Contract Management  
✅ **Module 13**: Communication Logging  

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ 
- PostgreSQL 14+
- npm or yarn

### Installation

1. **Clone & Install Dependencies**
```bash
npm install
```

2. **Setup Environment**
```bash
cp .env.example .env
# Edit .env with your database credentials
```

3. **Setup Database**
```bash
# Generate Prisma Client
npm run prisma:generate

# Run migrations
npm run prisma:migrate

# (Optional) Open Prisma Studio to view data
npm run prisma:studio
```

4. **Start Development Server**
```bash
npm run dev
```

Server will run on `http://localhost:3000`

## 📁 Project Structure

```
paymedia-crm/
├── prisma/
│   ├── schema.prisma          # Database schema (Phase 1 complete)
│   └── migrations/            # Database migrations
├── src/
│   ├── controllers/           # Route controllers
│   ├── middleware/            # Auth, RBAC, validation
│   ├── routes/                # API routes
│   ├── services/              # Business logic
│   ├── utils/                 # Helper functions
│   └── server.js              # Entry point
├── .env.example               # Environment template
└── package.json
```

## 🔐 Security Features

- JWT-based authentication
- MFA support (planned)
- Role-Based Access Control (RBAC)
- Immutable audit logs for compliance
- 7-year data retention policy

## 📊 Database Schema Highlights

### Key Features:
- **Versioning**: All proposals, agreements, and POs support version history
- **Soft Deletes**: `is_deleted` flag on all tables
- **Audit Trail**: `created_by`, `modified_by`, `created_at`, `updated_at` on all entities
- **Relational Integrity**: Organisation → Contact → Lead → Opportunity → Proposal → Agreement → PO

## 🛠️ Available Scripts

```bash
npm run dev              # Start development server with nodemon
npm start                # Start production server
npm run prisma:generate  # Generate Prisma Client
npm run prisma:migrate   # Run database migrations
npm run prisma:studio    # Open Prisma Studio (DB GUI)
npm test                 # Run tests
```

## 🔄 Next Steps (Upcoming Phases)

**Phase 2**: Project Delivery Modules (Onboarding, Project Docs, Resources, Risk, Payments)  
**Phase 3**: Intelligence & Experience (Support, Knowledge Base, Customer Portal, Notifications)  
**Step 6**: Complete Security & Audit Implementation

## 📖 API Documentation

API routes will follow RESTful conventions:

```
GET    /api/v1/organisations
POST   /api/v1/organisations
GET    /api/v1/organisations/:id
PUT    /api/v1/organisations/:id
DELETE /api/v1/organisations/:id  (soft delete)
```

Full API documentation will be available after Step 2 implementation.

## 👥 User Roles

- **ADMIN**: Full system access
- **EXECUTIVE**: Dashboard and reporting access
- **SALES**: Lead, Opportunity, Proposal management
- **LEGAL**: Agreement and Contract management
- **FINANCE**: PO, Payment, and Financial tracking
- **PROJECT_MANAGER**: Project lifecycle management
- **SUPPORT**: Post-go-live support and tickets

## 📝 License

Proprietary - PayMedia Inc.

---

**Built with ❤️ for Enterprise Project Management**
