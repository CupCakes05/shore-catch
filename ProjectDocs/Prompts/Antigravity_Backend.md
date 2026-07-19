# Muciriz Backend — Antigravity Build Prompt

> ⚠️ BUILD EXACTLY WHAT IS SPECIFIED. Do not add extra services, microservices, or features not described here. This is a monolithic Node.js API serving 3 client apps.

---

## PROJECT OVERVIEW

**Muciriz** is a local fresh-goods ecommerce platform in Kerala, India (fish, vegetables, meat). It has:
- 1 Admin Panel (Next.js web app)
- 1 Staff App (Flutter mobile)
- 1 Customer App (Flutter mobile)
- **1 Shared Backend API** ← YOU ARE BUILDING THIS

The backend is a single Node.js REST API that all 3 apps consume. The full API contract (every endpoint, request/response schema) is defined in the file `ProjectDocs/08_APIContracts.md`. Follow it exactly.

---

## TECH STACK (Use Exactly These)

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Runtime | Node.js 20 LTS | Server runtime |
| Language | TypeScript (strict mode) | Type safety |
| Framework | Express.js | HTTP routing, middleware |
| Database | Supabase (PostgreSQL) | Primary data store, Row Level Security |
| Auth | Supabase Auth + custom JWT | Session management, role-based tokens |
| File Storage | AWS S3 | Product images, category images, offer banners, receipts |
| Push Notifications | Firebase Cloud Messaging (FCM) | Push to mobile apps |
| SMS/WhatsApp OTP | AWS SNS | OTP delivery (SMS fallback) |
| WhatsApp Business API | Meta Cloud API | Primary OTP delivery channel |
| PDF Generation | @react-pdf/renderer or PDFKit | Invoice generation |
| Validation | Zod | Request schema validation |
| ORM/Query | Drizzle ORM | Type-safe SQL queries |
| Caching | Node-cache (in-memory) | Config, frequently accessed data |
| Logging | Pino | Structured JSON logging |
| Process Manager | PM2 | Production process management |
| Containerization | Docker + Docker Compose | Portable deployment |
| Testing | Vitest + Supertest | Unit + integration tests |

---

## PROJECT STRUCTURE

```
muciriz-backend/
├── docker/
│   ├── Dockerfile
│   ├── Dockerfile.dev
│   └── .dockerignore
├── docker-compose.yml
├── docker-compose.dev.yml
├── src/
│   ├── index.ts                    # Entry point
│   ├── app.ts                      # Express app setup
│   ├── config/
│   │   ├── env.ts                  # Environment validation (Zod)
│   │   ├── supabase.ts             # Supabase client init
│   │   ├── s3.ts                   # AWS S3 client
│   │   ├── firebase.ts             # FCM init
│   │   ├── sns.ts                  # AWS SNS client
│   │   └── whatsapp.ts             # Meta WhatsApp API client
│   ├── db/
│   │   ├── schema/                 # Drizzle schema definitions
│   │   │   ├── auth.ts
│   │   │   ├── locations.ts
│   │   │   ├── categories.ts
│   │   │   ├── products.ts
│   │   │   ├── orders.ts
│   │   │   ├── staff.ts
│   │   │   ├── customers.ts
│   │   │   ├── offers.ts
│   │   │   ├── notifications.ts
│   │   │   ├── stock.ts
│   │   │   ├── purchases.ts
│   │   │   ├── misc-purchases.ts
│   │   │   ├── delivery-times.ts
│   │   │   └── config.ts
│   │   ├── migrations/             # Drizzle migrations
│   │   └── index.ts                # DB connection + Drizzle instance
│   ├── middleware/
│   │   ├── auth.ts                 # JWT verification + role extraction
│   │   ├── requireRole.ts          # Role guard (admin/staff/customer)
│   │   ├── requirePermission.ts    # Staff permission guard
│   │   ├── locationScope.ts        # Staff location scoping
│   │   ├── validate.ts             # Zod validation middleware
│   │   ├── upload.ts               # Multer config for file uploads
│   │   ├── rateLimiter.ts          # Rate limiting (OTP, login)
│   │   ├── errorHandler.ts         # Global error handler
│   │   └── requestLogger.ts        # Pino request logging
│   ├── routes/
│   │   ├── index.ts                # Route aggregator
│   │   ├── auth.routes.ts
│   │   ├── locations.routes.ts
│   │   ├── categories.routes.ts
│   │   ├── products.routes.ts
│   │   ├── orders.routes.ts
│   │   ├── staff.routes.ts
│   │   ├── customers.routes.ts
│   │   ├── offers.routes.ts
│   │   ├── notifications.routes.ts
│   │   ├── stock.routes.ts
│   │   ├── purchases.routes.ts
│   │   ├── misc-purchases.routes.ts
│   │   ├── delivery-times.routes.ts
│   │   ├── reports.routes.ts
│   │   ├── config.routes.ts
│   │   └── uploads.routes.ts
│   ├── controllers/                # Request handling (thin layer)
│   │   ├── auth.controller.ts
│   │   ├── locations.controller.ts
│   │   ├── categories.controller.ts
│   │   ├── products.controller.ts
│   │   ├── orders.controller.ts
│   │   ├── staff.controller.ts
│   │   ├── customers.controller.ts
│   │   ├── offers.controller.ts
│   │   ├── notifications.controller.ts
│   │   ├── stock.controller.ts
│   │   ├── purchases.controller.ts
│   │   ├── misc-purchases.controller.ts
│   │   ├── delivery-times.controller.ts
│   │   ├── reports.controller.ts
│   │   ├── config.controller.ts
│   │   └── uploads.controller.ts
│   ├── services/                   # Business logic
│   │   ├── auth.service.ts
│   │   ├── otp.service.ts
│   │   ├── locations.service.ts
│   │   ├── categories.service.ts
│   │   ├── products.service.ts
│   │   ├── orders.service.ts
│   │   ├── invoice.service.ts
│   │   ├── staff.service.ts
│   │   ├── customers.service.ts
│   │   ├── offers.service.ts
│   │   ├── notifications.service.ts
│   │   ├── stock.service.ts
│   │   ├── purchases.service.ts
│   │   ├── misc-purchases.service.ts
│   │   ├── delivery-times.service.ts
│   │   ├── reports.service.ts
│   │   ├── config.service.ts
│   │   └── upload.service.ts
│   ├── validators/                 # Zod schemas per resource
│   │   ├── auth.schema.ts
│   │   ├── locations.schema.ts
│   │   ├── categories.schema.ts
│   │   ├── products.schema.ts
│   │   ├── orders.schema.ts
│   │   ├── staff.schema.ts
│   │   ├── offers.schema.ts
│   │   └── ...
│   ├── utils/
│   │   ├── pagination.ts           # Pagination helpers
│   │   ├── gst.ts                  # GST calculation (back-calc from inclusive)
│   │   ├── orderNumber.ts          # M{YEAR}#{5-digit} generator
│   │   ├── errors.ts               # Custom error classes
│   │   └── response.ts             # Standard response formatter
│   └── types/
│       ├── express.d.ts            # Express request augmentation
│       └── index.ts                # Shared types
├── tests/
│   ├── setup.ts
│   ├── auth.test.ts
│   ├── orders.test.ts
│   └── ...
├── .env.example
├── .gitignore
├── tsconfig.json
├── drizzle.config.ts
├── vitest.config.ts
├── package.json
├── pm2.config.js
└── README.md
```

---

## DATABASE SCHEMA (Supabase PostgreSQL)

Design tables for these entities. Use UUIDs for primary keys. Add `created_at`, `updated_at` timestamps on all tables.

### Tables Required:

```sql
-- 1. admins (id, name, email, password_hash, role, created_at)
-- 2. staff (id, name, phone, password_hash, is_active, created_at)
-- 3. staff_locations (staff_id FK, location_id FK) — many-to-many
-- 4. staff_permissions (staff_id FK, permission_key, value boolean)
-- 5. staff_allowed_categories (staff_id FK, category_id FK)
-- 6. customers (id, name, whatsapp_number UNIQUE, delivery_address, nearest_landmark, location_id FK, is_verified, created_at)
-- 7. locations (id, name UNIQUE, is_active, created_at)
-- 8. categories (id, name, image_url, sort_order, is_active, created_at)
-- 9. category_locations (category_id FK, location_id FK) — many-to-many
-- 10. products (id, name, category_id FK, gross_weight, net_weight, weight_unit, price DECIMAL, gst_applicable, gst_percent, image_url, is_active, approval_status ENUM, submitted_by FK nullable, approved_by FK nullable, approved_at, created_by, created_at)
-- 11. product_locations (product_id FK, location_id FK) — many-to-many
-- 12. product_recommendations (id, product_id FK, recommended_product_id FK, sort_order)
-- 13. category_recommendations (id, category_id FK, recommended_product_id FK, sort_order)
-- 14. stock (product_id FK, location_id FK, count INT, last_updated_by FK, last_updated_at) — composite PK
-- 15. delivery_times (id, label, location_id FK nullable, is_active, created_at)
-- 16. orders (id, order_number UNIQUE, customer_id FK, location_id FK, state ENUM, is_pre_order, sale_type ENUM, placed_by ENUM, placed_by_staff_id FK nullable, delivery_date DATE, delivery_time_id FK, delivery_address_snapshot, payment_method_preference, payment_method_collected, subtotal, gst_total, total, shop_sale_phone, shop_sale_customer_name, created_at, updated_at)
-- 17. order_items (id, order_id FK, product_id FK, product_name_snapshot, gross_weight_snapshot, net_weight_snapshot, unit_price_snapshot, gst_applicable_snapshot, gst_percent_snapshot, gst_amount_snapshot, quantity, line_total, line_total_with_gst)
-- 18. order_timeline (id, order_id FK, state, acted_by_id FK, acted_by_name, acted_at)
-- 19. offers (id, title, message, target_type ENUM, target_id nullable, discount_percent, banner_image_url, location_id FK nullable, starts_at, ends_at, is_active, created_at)
-- 20. notifications (id, customer_id FK nullable, title, message, type ENUM, related_order_id FK nullable, related_offer_id FK nullable, is_read, created_at)
-- 21. purchases (id, product_id FK, location_id FK, purchase_rate DECIMAL, gross_weight, net_weight, quantity_purchased, supplier_name, purchase_date DATE, recorded_by FK, created_at)
-- 22. misc_purchases (id, item_name, quantity, cost DECIMAL, purchase_date DATE, location_id FK, notes, receipt_image_url, staff_id FK, created_at)
-- 23. system_config (key VARCHAR PK, value TEXT, updated_at, updated_by FK)
-- 24. order_number_sequence (year INT PK, last_number INT) — for M{YEAR}#XXXXX
```

### ENUMs:
```sql
CREATE TYPE approval_status AS ENUM ('PendingApproval', 'Approved', 'Rejected');
CREATE TYPE order_state AS ENUM ('Pending', 'OrderConfirmed', 'Delivered', 'Completed', 'Cancelled');
CREATE TYPE sale_type AS ENUM ('regular', 'shopSale');
CREATE TYPE placed_by AS ENUM ('customer', 'staff');
CREATE TYPE offer_target AS ENUM ('All', 'Category', 'Product');
CREATE TYPE notification_type AS ENUM ('OrderStatus', 'Offer', 'System');
CREATE TYPE payment_method AS ENUM ('GpayOnDelivery', 'CashOnDelivery', 'Gpay', 'Cash');
```

### Key Indexes:
```sql
CREATE INDEX idx_orders_state ON orders(state);
CREATE INDEX idx_orders_location ON orders(location_id);
CREATE INDEX idx_orders_customer ON orders(customer_id);
CREATE INDEX idx_orders_delivery_date ON orders(delivery_date);
CREATE INDEX idx_products_category ON products(category_id);
CREATE INDEX idx_products_approval ON products(approval_status);
CREATE INDEX idx_stock_location ON stock(location_id);
CREATE INDEX idx_customers_whatsapp ON customers(whatsapp_number);
CREATE INDEX idx_notifications_customer ON notifications(customer_id, is_read);
```

---

## AUTHENTICATION & AUTHORIZATION

### JWT Strategy:
- Use Supabase Auth for token issuance and verification.
- Custom claims in JWT: `{ role: "admin"|"staff"|"customer", userId: "...", locationIds?: [...], permissions?: {...} }`
- Token expiry: 7 days (mobile apps), 24 hours (admin panel).
- Refresh tokens handled by Supabase Auth SDK.

### Auth Flows:

**Customer (OTP-based):**
1. Customer registers → backend stores customer in DB → sends OTP via WhatsApp Business API
2. If WhatsApp fails → fallback to AWS SNS (SMS)
3. Customer verifies OTP → Supabase Auth creates user → returns JWT
4. Returning customers: phone → OTP → login (no password)

**Staff (Phone + Password):**
1. Admin creates staff account with phone + password
2. Staff logs in with phone + password → JWT with permissions embedded

**Admin (Email + Password):**
1. Pre-seeded admin account(s) in DB
2. Email + password login → JWT with role=admin
3. Forgot password → email reset link (Supabase Auth)

### Middleware Chain:
```
auth.ts → verifies JWT, extracts user object, attaches to req.user
requireRole('admin') → checks req.user.role
requirePermission('confirmOrder') → checks staff permissions
locationScope() → filters queries to staff's assigned locations
```

### Staff Permissions (9 flags):
```typescript
interface StaffPermissions {
  viewOrders: boolean;
  restrictToCategories: boolean;  // if true, check allowedCategoryIds
  addProducts: boolean;
  confirmOrder: boolean;
  confirmDelivery: boolean;
  confirmPayment: boolean;
  updateStock: boolean;
  recordMiscPurchase: boolean;
  placeShopSale: boolean;
}
```

---

## ORDER LIFECYCLE & STATE MACHINE

```
Pending → OrderConfirmed → Delivered → Completed
   ↓                                        ↑
Cancelled ←────────────────────────────────┘ (payment confirm triggers)
```

### Rules:
- **POST /orders** (Customer) → state = `Pending`, stock decremented immediately
- **PATCH /orders/{id}/state** `{state: "OrderConfirmed"}` → requires `confirmOrder` permission
- **PATCH /orders/{id}/state** `{state: "Delivered"}` → requires `confirmDelivery` permission
- **PATCH /orders/{id}/payment** `{collectedMethod: "Gpay"|"Cash"}` → state becomes `Completed`, requires `confirmPayment`
- **POST /orders/{id}/cancel** (Customer) → only from `Pending`, stock restored
- **POST /orders/{id}/staff-cancel** (Staff) → from any state < Completed, stock restored
- **Every state change** → insert into `order_timeline` with timestamp + actor

### Stock Decrement/Restore:
- On order placed: `stock.count -= item.quantity` for each item at order's location
- On cancel: `stock.count += item.quantity` (restore)
- If stock < 0 after decrement → return 409 with "Insufficient stock" error

### Pre-Orders:
- If `deliveryDate` is a future date → `is_pre_order = true`
- Pre-orders are just regular orders with a future delivery date. No special logic.

### Shop Sales (Staff placing order for walk-in customer):
- POST /orders/shop-sale → state immediately = `Completed`
- No fulfillment lifecycle. Stock decremented. Invoice generated.
- `sale_type = "shopSale"`, `placed_by = "staff"`

---

## INVOICE GENERATION

### Trigger:
- Auto-generate PDF when order is created (POST /orders or POST /orders/shop-sale)
- Store PDF in AWS S3: `invoices/{year}/{order_number}.pdf`
- Return download URL via GET /orders/{id}/invoice

### Invoice Content:
- Muciriz branding (logo, company name, address)
- Invoice number = order number (M2026#00015)
- Customer details (name, address, landmark, location)
- Itemized table: Product | Weight | Qty | Unit Price | GST % | GST ₹ | Line Total
- GST summary grouped by rate (5%, 12%, 18%, etc.)
- Subtotal (before GST) + GST Total + Grand Total
- Payment method, delivery date, time slot
- Generated timestamp

### GST Calculation (Products store inclusive prices):
```typescript
function calculateGST(inclusivePrice: number, gstPercent: number) {
  const basePrice = inclusivePrice / (1 + gstPercent / 100);
  const gstAmount = inclusivePrice - basePrice;
  return { basePrice: round2(basePrice), gstAmount: round2(gstAmount) };
}
```

---

## FILE UPLOADS (AWS S3)

### Config:
- Bucket: `muciriz-assets` (single bucket, organized by prefix)
- Prefixes: `products/`, `categories/`, `offers/`, `receipts/`, `invoices/`
- ACL: private (serve via signed URLs or CloudFront)
- Max file size: 5MB
- Accepted types: JPEG, PNG, WebP

### Upload Flow:
1. Client sends `multipart/form-data` to `POST /uploads`
2. Multer receives file in memory (no disk write)
3. Validate file type + size
4. Upload to S3 with content-type metadata
5. Return permanent CDN URL

---

## NOTIFICATIONS

### Push (Firebase Cloud Messaging):
- Store FCM device tokens per customer (register on app login)
- On order state change → send push to customer's device
- On new offer created by admin → bulk push to location's customers

### In-App:
- Every notification → insert into `notifications` table
- Customer reads via GET /notifications

### OTP Delivery:
1. **Primary:** WhatsApp Business API (Meta Cloud API) — template message with OTP
2. **Fallback:** AWS SNS SMS — if WhatsApp delivery fails after 10 seconds

### Admin Broadcast:
- POST /notifications → send to all customers in a location (or all)
- Channels: in-app always + FCM push + optional SMS

---

## DOCKER CONFIGURATION

### Dockerfile (Production):
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
RUN apk add --no-cache dumb-init
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./
COPY --from=builder /app/drizzle.config.ts ./
COPY --from=builder /app/src/db/migrations ./src/db/migrations

ENV NODE_ENV=production
EXPOSE 3000

USER node
ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "dist/index.js"]
```

### Dockerfile.dev (Development):
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

### docker-compose.yml (Production):
```yaml
version: '3.8'

services:
  api:
    build:
      context: .
      dockerfile: docker/Dockerfile
    ports:
      - "3000:3000"
    env_file:
      - .env
    environment:
      - NODE_ENV=production
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "--no-verbose", "--tries=1", "--spider", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: '1.0'
```

### docker-compose.dev.yml (Development with local Postgres):
```yaml
version: '3.8'

services:
  api:
    build:
      context: .
      dockerfile: docker/Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    env_file:
      - .env.dev
    environment:
      - NODE_ENV=development
    depends_on:
      db:
        condition: service_healthy
    command: npm run dev

  db:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: muciriz_dev
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 3s
      retries: 5

volumes:
  pgdata:
```

### .dockerignore:
```
node_modules
dist
.git
.env
*.log
coverage
.DS_Store
```

---

## ENVIRONMENT VARIABLES

### .env.example:
```bash
# Server
NODE_ENV=production
PORT=3000
API_VERSION=v1
CORS_ORIGINS=https://admin.muciriz.in,https://api.muciriz.in

# Supabase
SUPABASE_URL=https://xxxxx.supabase.co
SUPABASE_ANON_KEY=eyJxxx...
SUPABASE_SERVICE_ROLE_KEY=eyJxxx...
DATABASE_URL=postgresql://postgres:password@db.xxxxx.supabase.co:5432/postgres

# JWT
JWT_SECRET=your-jwt-secret-min-32-chars
JWT_EXPIRY_MOBILE=7d
JWT_EXPIRY_WEB=24h

# AWS S3
AWS_REGION=ap-south-1
AWS_ACCESS_KEY_ID=AKIA...
AWS_SECRET_ACCESS_KEY=xxx
AWS_S3_BUCKET=muciriz-assets
AWS_CLOUDFRONT_URL=https://cdn.muciriz.in

# AWS SNS (SMS fallback)
AWS_SNS_REGION=ap-south-1

# WhatsApp Business API (Meta Cloud)
WHATSAPP_PHONE_NUMBER_ID=1234567890
WHATSAPP_ACCESS_TOKEN=EAAxxxxx
WHATSAPP_OTP_TEMPLATE_NAME=muciriz_otp

# Firebase (FCM Push)
FIREBASE_PROJECT_ID=muciriz-app
FIREBASE_CLIENT_EMAIL=firebase-adminsdk@muciriz-app.iam.gserviceaccount.com
FIREBASE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n..."

# Rate Limiting
OTP_RATE_LIMIT_WINDOW_MS=300000
OTP_RATE_LIMIT_MAX=3
LOGIN_RATE_LIMIT_WINDOW_MS=900000
LOGIN_RATE_LIMIT_MAX=5

# Invoice
COMPANY_NAME=Muciriz Fresh Goods
COMPANY_ADDRESS=Aluva, Kerala, India
COMPANY_PHONE=+919876500000
COMPANY_GSTIN=32XXXXX1234X1ZX
```

---

## API CONTRACTS REFERENCE

The complete API contract with every endpoint's request/response schema is defined in `ProjectDocs/08_APIContracts.md`. That document contains:

- **17 sections, ~60 endpoints** with full JSON request/response schemas
- **Auth matrix** showing which role can access what
- **Order state machine** with transition rules
- **Invoice number format** (M{YEAR}#{5-digit})
- **GST calculation** formula

Implement EVERY endpoint from that document. The sections are:
1. Authentication & Session (9 endpoints)
2. Service Locations (4 endpoints)
3. Categories (6 endpoints)
4. Products (8 endpoints)
5. Stock & Inventory (4 endpoints)
6. Delivery Times (4 endpoints)
7. Orders (9 endpoints)
8. Staff Management (5 endpoints)
9. Customers (3 endpoints)
10. Offers (4 endpoints)
11. Notifications (3 endpoints)
12. Miscellaneous Purchases (2 endpoints)
13. Reports (6 endpoints)
14. System Configuration (2 endpoints)
15. File Uploads (1 endpoint)
16. Customer Profile (2 endpoints)
17. Admin Profile (2 endpoints)

---

## CRITICAL BUSINESS RULES

1. **Location scoping is mandatory.** Staff see only data from their assigned locations. Customers see only products/categories available in their location.

2. **Staff permission checks are granular.** Every staff action must check their specific permission flag. Don't just check "is staff" — check "has confirmOrder permission".

3. **Category restriction for staff.** If `restrictToCategories = true`, staff only see orders containing products from their `allowedCategoryIds`.

4. **Product approval workflow.** Staff-added products enter `PendingApproval`. Admin auto-approves. Admin can approve/reject pending products.

5. **Stock is location-scoped.** Each product has separate stock counts per location. Stock is decremented on order creation, restored on cancellation.

6. **Order snapshots.** When an order is placed, snapshot product name, weight, price, and GST into `order_items`. Never reference live product data for historical orders.

7. **Invoice auto-generation.** Every completed order (and every shop sale) gets a PDF invoice stored in S3.

8. **Delivery times resolution.** If a location has per-location delivery times, use those. Otherwise fall back to global delivery times.

9. **Pre-orders are just future-dated orders.** No separate logic. Just a boolean flag (`is_pre_order`) set when `delivery_date > today`.

10. **Soft deletes everywhere.** Never hard-delete data. Set `is_active = false`.

11. **Profit/Loss is simplified.** Profit = Total Sales - (Purchase Records + Misc Purchases). No COGS per item.

12. **Order numbering.** Format: `M{YEAR}#{5-digit-padded}`. Resets annually. Use `order_number_sequence` table with row lock for thread safety.

---

## SECURITY REQUIREMENTS

1. **Input validation on every endpoint.** Use Zod schemas. Reject unexpected fields.
2. **SQL injection prevention.** Use Drizzle ORM parameterized queries. Never concatenate user input into SQL.
3. **Rate limiting.** OTP endpoints: 3 requests per 5 minutes per phone. Login: 5 attempts per 15 minutes.
4. **File upload validation.** Check MIME type (not just extension), enforce 5MB limit, sanitize filename.
5. **CORS.** Only allow configured origins (admin panel domain). Mobile apps don't need CORS.
6. **Helmet.js.** Set security headers (X-Frame-Options, CSP, etc.).
7. **No secrets in responses.** Never return password hashes, internal IDs unnecessarily, or raw stack traces.
8. **Request size limits.** JSON body: 1MB max. File uploads: 5MB max.

---

## SCRIPTS (package.json)

```json
{
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "start:pm2": "pm2 start pm2.config.js",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:push": "drizzle-kit push",
    "db:studio": "drizzle-kit studio",
    "db:seed": "tsx src/db/seed.ts",
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage",
    "lint": "eslint src/",
    "lint:fix": "eslint src/ --fix",
    "docker:dev": "docker compose -f docker-compose.dev.yml up --build",
    "docker:prod": "docker compose up --build -d",
    "docker:down": "docker compose down"
  }
}
```

---

## SEED DATA

Create a seed script (`src/db/seed.ts`) that populates:

- 1 Admin: `admin@muciriz.in` / `Admin123!`
- 4 Locations: Aluva, Perumbavoor, Kalady, Angamaly
- 4 Categories: Fresh Fish, Vegetables, Meat & Poultry, Prawns & Crabs (each assigned to all locations)
- 10 Products: Karimeen ₹320, Seer Fish ₹850, Chicken Breast ₹180, Prawns Large ₹420, Lady's Finger ₹35, Tomato ₹25, Mutton ₹650, Crab ₹380, Pomfret ₹450, Drumstick ₹30
- 4 Staff members with varied permissions
- 3 Delivery Times: Morning 7-9am, Morning 9-11am, Evening 4-6pm
- 5 Sample customers
- 10 Sample orders (mixed states)
- 2 Active offers
- System config: GPay UPI ID, merchant name, customer care numbers
- Stock entries for all products at all locations

---

## HEALTH CHECK ENDPOINT

```
GET /health
Response: { "status": "ok", "timestamp": "2026-07-19T10:00:00Z", "version": "1.0.0" }
```

```
GET /health/ready
Response: { "status": "ok", "database": "connected", "s3": "reachable" }
```

---

## ERROR HANDLING PATTERN

```typescript
// Custom error class
class AppError extends Error {
  constructor(
    public statusCode: number,
    public code: string,
    message: string,
    public fields?: string[]
  ) {
    super(message);
  }
}

// Global error handler (middleware/errorHandler.ts)
function errorHandler(err, req, res, next) {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      error: { code: err.code, message: err.message, fields: err.fields }
    });
  }
  // Log unexpected errors
  logger.error(err);
  return res.status(500).json({
    error: { code: "SERVER_ERROR", message: "Internal server error" }
  });
}
```

---

## DEPLOYMENT NOTES

1. **Run anywhere with Docker.** `docker compose up --build` should start the full API.
2. **Database migrations.** Run `npm run db:migrate` before first start (or in Docker entrypoint).
3. **Supabase is the hosted Postgres.** In production, connect to Supabase's hosted DB. In dev, use local Postgres via Docker Compose.
4. **AWS Region.** Use `ap-south-1` (Mumbai) for lowest latency to Kerala.
5. **PM2 for non-Docker deployments.** If running on a VM directly, use PM2 for process management + auto-restart.
6. **Logging.** Pino structured JSON logs. In production, pipe to log aggregator.

---

## WHAT NOT TO BUILD

- ❌ No GraphQL. REST only.
- ❌ No WebSockets. Polling or FCM push for real-time.
- ❌ No microservices. Single monolithic API.
- ❌ No Redis. In-memory cache (node-cache) is sufficient for this scale.
- ❌ No message queues. Direct service calls.
- ❌ No API gateway. Express handles everything.
- ❌ No complex RBAC library. Custom middleware is simpler for 3 fixed roles.
- ❌ No email service (except admin password reset via Supabase Auth built-in).

---

## BUILD ORDER (Implement in This Sequence)

1. **Project setup** — Initialize Node.js + TypeScript + Express + Docker
2. **Database** — Drizzle schema + migrations + Supabase connection
3. **Auth module** — Login (all 3 roles) + JWT + middleware
4. **Locations CRUD** — Simplest module, validates full stack works
5. **Categories CRUD** — Multi-location, image upload to S3
6. **Products CRUD** — Approval workflow, GST, multi-location, stock init
7. **Delivery Times CRUD** — Global vs per-location logic
8. **Staff CRUD** — Permissions, password management
9. **Orders** — Full lifecycle, state machine, stock decrement/restore, snapshots
10. **Invoice generation** — PDF creation, S3 storage
11. **Offers CRUD** — Targeting logic
12. **Notifications** — FCM push + in-app + WhatsApp OTP
13. **Stock & Inventory** — Quick update, purchase records
14. **Misc Purchases** — Staff expense tracking
15. **Reports** — Sales, profit/loss, staff performance, aggregations
16. **System Config** — Key-value store for UPI/phone settings
17. **Customer management** — Admin search/edit
18. **Seed script** — Populate test data
19. **Docker finalize** — Ensure `docker compose up` works end-to-end
20. **API testing** — Vitest + Supertest for critical paths

---

## FINAL REMINDERS

- Follow `ProjectDocs/08_APIContracts.md` for EXACT request/response shapes. Don't invent new fields.
- Every list endpoint needs pagination (page, limit, total, totalPages).
- Currency is ₹ (Indian Rupees). Stored as DECIMAL(10,2).
- Timezone: Asia/Kolkata (IST, UTC+5:30).
- Phone numbers: E.164 format (+91XXXXXXXXXX).
- This serves ~500 customers in 4 small towns. Don't over-engineer for scale.
- Keep it simple, clean, and working. Production-ready > feature-rich.

---

*End of Antigravity Backend Prompt*
