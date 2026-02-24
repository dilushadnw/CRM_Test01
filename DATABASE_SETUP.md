# Database Setup Guide

## Step-by-Step PostgreSQL Setup

### 1. Install PostgreSQL

**Windows:**
- Download from: https://www.postgresql.org/download/windows/
- Use the installer and follow the wizard
- Remember the password you set for 'postgres' user

**macOS:**
```bash
brew install postgresql@14
brew services start postgresql@14
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
```

### 2. Create Database

Open PostgreSQL terminal:

**Windows:**
- Open "pgAdmin" or "SQL Shell (psql)"

**macOS/Linux:**
```bash
psql -U postgres
```

Then run:
```sql
CREATE DATABASE paymedia_crm;
CREATE USER paymedia_user WITH PASSWORD 'your_secure_password';
GRANT ALL PRIVILEGES ON DATABASE paymedia_crm TO paymedia_user;
\q
```

### 3. Update .env File

Copy the example environment file:
```bash
cp .env.example .env
```

Edit `.env` and update the DATABASE_URL:
```
DATABASE_URL="postgresql://paymedia_user:your_secure_password@localhost:5432/paymedia_crm?schema=public"
```

### 4. Install Node Dependencies

```bash
npm install
```

### 5. Generate Prisma Client

```bash
npm run prisma:generate
```

This creates the Prisma Client based on your schema.

### 6. Run Database Migrations

```bash
npm run prisma:migrate
```

When prompted for a migration name, enter: `init_phase1_schema`

This will:
- Create all tables in your PostgreSQL database
- Set up indexes, relations, and constraints
- Record the migration in `prisma/migrations/`

### 7. (Optional) View Your Database

Open Prisma Studio - a visual database browser:
```bash
npm run prisma:studio
```

Access at: http://localhost:5555

### 8. Start the Development Server

```bash
npm run dev
```

Server will be available at: http://localhost:3000

## Verify Setup

Test the health endpoint:
```bash
curl http://localhost:3000/health
```

Expected response:
```json
{
  "status": "OK",
  "message": "PayMedia CRM System is running",
  "timestamp": "2026-02-24T...",
  "version": "1.0.0"
}
```

## Database Schema Overview

### Phase 1 Tables Created:

1. **users** - System users with RBAC
2. **organisations** - Company profiles with hierarchy
3. **contacts** - Contact persons linked to organisations
4. **leads** - Lead management with scoring
5. **opportunities** - Sales pipeline opportunities
6. **proposals** - Versioned proposals with approval workflow
7. **purchase_orders** - PO tracking and utilization
8. **agreements** - Contracts (MSA, SOW, NDA) with version control
9. **communications** - Email, call, meeting logs
10. **audit_logs** - Immutable audit trail (compliance)

## Common Issues & Solutions

### Issue: "Connection refused"
**Solution:** Ensure PostgreSQL is running
```bash
# macOS
brew services list

# Linux
sudo systemctl status postgresql

# Windows
# Check Services app for "postgresql" service
```

### Issue: "Authentication failed"
**Solution:** Check your DATABASE_URL credentials match what you created

### Issue: "Database does not exist"
**Solution:** Run the CREATE DATABASE command again

### Issue: "Migration failed"
**Solution:** 
1. Drop the database and recreate it
2. Delete `prisma/migrations` folder
3. Run `npm run prisma:migrate` again

## Next Steps

After the database is set up:
1. Review the schema in Prisma Studio
2. Proceed to **Step 2**: API Layer & RBAC implementation
3. Create seed data for testing (optional)

## Prisma Commands Reference

```bash
# Generate Prisma Client (after schema changes)
npm run prisma:generate

# Create a new migration
npm run prisma:migrate

# Reset database (WARNING: Deletes all data)
npx prisma migrate reset

# Open Prisma Studio
npm run prisma:studio

# Format schema file
npx prisma format

# Validate schema
npx prisma validate
```

## Production Considerations

For production deployment:
1. Use strong passwords
2. Enable SSL for database connections
3. Set up database backups (7-year retention as per BRD)
4. Use connection pooling
5. Configure proper user permissions
6. Enable audit logging
7. Set up monitoring and alerts

---

**Need Help?** Check the main README.md or Prisma documentation: https://www.prisma.io/docs
