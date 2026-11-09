# SokoDirect Kenya — backend starter

A Phase 1 API starter for the Lodwar/Turkana agricultural marketplace.

## Included
- Farmer, wholesaler, retailer and buyer registration
- Email/password login with bcrypt password hashing and JWT sessions
- PostgreSQL schema for users, categories and produce listings
- Public listing search by keyword, category and location
- Signed-in sellers can publish and pause their own listings
- Admin summary, user list and account activation controls
- Basic security middleware (Helmet), JSON size limit and parameterized SQL

## Not included yet
Real WhatsApp messaging, in-site chat, M-Pesa payments, delivery booking, seller verification workflow, reviews, image upload service, production hosting, or a finished admin web interface. The API only creates the foundation for these.

## Requirements
- Node.js 18 or newer
- PostgreSQL 14 or newer

## Run locally
1. Install Node.js and PostgreSQL.
2. Create a database named `sokodirect`.
3. In a terminal, open this project folder and run `npm install`.
4. Copy `.env.example` to `.env`.
5. Edit `.env`: set your PostgreSQL password and a long random `JWT_SECRET`.
6. Run the database schema:
   `psql -d sokodirect -f db/schema.sql`
7. Start the API:
   `npm run dev`
8. Open `http://localhost:5000/api/health` in your browser. You should see a JSON status response.

## Main API routes

### Authentication
- `POST /api/auth/register`
  JSON: `{ "fullName":"Amina", "email":"amina@example.com", "phone":"+2547...", "password":"use-a-strong-password", "role":"farmer", "preferredLanguage":"en" }`
- `POST /api/auth/login`
  JSON: `{ "email":"amina@example.com", "password":"use-a-strong-password" }`

### Listings
- `GET /api/listings`
- Search example: `GET /api/listings?search=tomato&location=Lodwar`
- Category example: `GET /api/listings?category=Fruits`
- `POST /api/listings` (Bearer token required)
  JSON: `{ "categoryId":1, "title":"Fresh maize", "description":"Harvested this week", "pricePerUnit":80, "unit":"kg", "quantity":100, "location":"Lodwar, Turkana" }`
- `PATCH /api/listings/:id/status` (owner's Bearer token required)
  JSON: `{ "status":"paused" }`

### Admin
All admin routes require a JWT belonging to a user whose role is `admin`.
- `GET /api/admin/summary`
- `GET /api/admin/users`
- `PATCH /api/admin/users/:id/active` with `{ "isActive": false }`

## Important admin setup
The first admin account must be created through a controlled database operation by the project owner. Register a normal account first, then promote its role manually using a trusted database connection:
`UPDATE users SET role='admin' WHERE email='owner@example.com';`

Do not expose database credentials or your `.env` file. Before public launch, add rate limiting, email/phone verification, password reset, audit logging, request validation, backups, monitoring, privacy/terms pages, and a security review.

## Business operating model
For the first launch, buyers and sellers contact each other directly. SokoDirect does not collect payments, hold customer funds, or guarantee delivery in this starter version. Confirm seller identity, stock, price, transport and payment terms independently before transacting.
