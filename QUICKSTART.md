# Quick Start - PayMedia CRM Setup

## ⚡ Fastest Way to Get Started

### 1. Install Dependencies
```powershell
npm install
```

### 2. Setup Environment
```powershell
# Copy environment template
Copy-Item .env.example .env

# Edit .env file and update DATABASE_URL with your PostgreSQL credentials
notepad .env
```

**Example DATABASE_URL:**
```
DATABASE_URL="postgresql://postgres:yourpassword@localhost:5432/paymedia_crm?schema=public"
```

### 3. Setup Database

If PostgreSQL is not installed:
- Download: https://www.postgresql.org/download/windows/
- Install and remember your password

Create the database:
```powershell
# Open PostgreSQL Shell (psql) and run:
# CREATE DATABASE paymedia_crm;
```

Or using psql command:
```powershell
psql -U postgres -c "CREATE DATABASE paymedia_crm;"
```

### 4. Generate Prisma Client & Run Migrations
```powershell
# Generate Prisma Client
npm run prisma:generate

# Create database tables
npm run prisma:migrate
```

When prompted for migration name, enter: `init`

### 5. Start Development Server
```powershell
npm run dev
```

✅ Server will run on: http://localhost:3000

### 6. Test the API
Open browser or use curl:
```powershell
# Health check
curl http://localhost:3000/health

# API info
curl http://localhost:3000/api/v1
```

## 📊 View Your Database

Open Prisma Studio (Database GUI):
```powershell
npm run prisma:studio
```

Access at: http://localhost:5555

## ✅ What's Included (Phase 1)

- ✅ PostgreSQL Database Schema (10 tables)
- ✅ 7 Core CRM Modules
- ✅ Version Control for Documents
- ✅ Audit Logging System
- ✅ Soft Delete Architecture
- ✅ Express Server with CORS & Security
- ✅ API Response Utilities
- ⏳ API Routes (Coming in Step 2)

## 📁 Project Structure

```
crmTest01/
├── prisma/
│   └── schema.prisma          ✅ Database schema
├── src/
│   ├── config/
│   │   └── database.js        ✅ Prisma client
│   ├── utils/
│   │   ├── ApiResponse.js     ✅ Response helper
│   │   └── AuditLogger.js     ✅ Audit utility
│   └── server.js              ✅ Express server
├── .env.example               ✅ Environment template
├── package.json               ✅ Dependencies
├── README.md                  ✅ Main documentation
├── DATABASE_SETUP.md          ✅ Setup guide
└── SCHEMA_DOCUMENTATION.md    ✅ Schema docs
```

## 🔧 Useful Commands

```powershell
# Development
npm run dev                    # Start with nodemon (auto-reload)
npm start                      # Start production server

# Database
npm run prisma:generate        # Generate Prisma Client
npm run prisma:migrate         # Create/run migrations
npm run prisma:studio          # Open database GUI

# Prisma Utilities
npx prisma format              # Format schema file
npx prisma validate            # Validate schema
npx prisma db push             # Push schema without migration (dev only)
```

## 🐛 Troubleshooting

### Problem: "Database connection failed"
**Solution:**
1. Check PostgreSQL is running
2. Verify DATABASE_URL in .env
3. Ensure database exists

### Problem: "Port 3000 already in use"
**Solution:**
Change PORT in .env:
```
PORT=3001
```

### Problem: "Prisma Client not generated"
**Solution:**
```powershell
npm run prisma:generate
```

## 📚 Documentation

- **README.md** - Overview and architecture
- **DATABASE_SETUP.md** - Detailed database setup
- **SCHEMA_DOCUMENTATION.md** - Complete schema reference
- **instruction.md** - Development roadmap

## 🚀 Next Steps

### Continue to Step 2: API Layer & RBAC
Once database is set up, you're ready for:
- RESTful API endpoints for all modules
- Role-Based Access Control middleware
- JWT Authentication
- Validation middleware

Ask to "Execute Step 2" when ready!

## 💡 Tips

1. **Use Prisma Studio** to explore your database visually
2. **Check logs** in the terminal for any errors
3. **Restart server** after changing .env
4. **Read SCHEMA_DOCUMENTATION.md** to understand data relationships

---

**Need Help?** Check the detailed guides:
- Full setup: `DATABASE_SETUP.md`
- Schema reference: `SCHEMA_DOCUMENTATION.md`
- Project overview: `README.md`

**Happy Coding! 🎉**
