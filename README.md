# SupplySync — Frontend Application

> React 19 + Vite 7 single-page application for **B2B supply-chain coordination**. Provides role-based Admin and Vendor dashboards, real-time purchase-order lifecycle management, inventory tracking, vendor compliance, and PDF report generation. Connects to the **SupplySync Spring Boot microservice backend** via a unified API Gateway on port `8000`.

---

## Table of Contents

1. [Tech Stack & Dependencies](#tech-stack--dependencies)
2. [Prerequisites](#prerequisites)
3. [Environment Variables](#environment-variables)
4. [Scripts](#scripts)
5. [Folder Structure](#folder-structure)
6. [Routing & Page Map](#routing--page-map)
7. [Component Tree Breakdown](#component-tree-breakdown)
8. [State Management (Zustand)](#state-management-zustand)
9. [Service Layer & API Flow](#service-layer--api-flow)
10. [Authentication Flow](#authentication-flow)
11. [Key Libraries Explained](#key-libraries-explained)
12. [Conventions & Standards](#conventions--standards)
13. [Known Unused Dependencies](#known-unused-dependencies)
14. [Quick-Start for New Developers](#quick-start-for-new-developers)

---

## Tech Stack & Dependencies

### Core

| Library | Version | Purpose |
|---------|---------|---------|
| **React** | `^19.2.0` | UI framework |
| **React DOM** | `^19.2.0` | DOM renderer |
| **Vite** | `^7.2.4` | Dev server + bundler (sub-second HMR) |
| **React Router** | `^7.11.0` | Client-side routing (`BrowserRouter`) |
| **Zustand** | `^5.0.9` | Global state management with `persist` middleware |
| **Axios** | `^1.13.2` | HTTP client with interceptors |
| **Tailwind CSS** | `^4.1.18` | Utility-first CSS framework |
| **Shadcn UI** | `^3.6.3` | Copy-paste Radix-based component primitives |

### UI & Visualization

| Library | Version | Purpose |
|---------|---------|---------|
| `@base-ui/react` | `^1.0.0` | Base unstyled UI primitives (Radix successor) |
| `recharts` | `2.15.4` | Chart components (Bar, Pie, Line) for dashboards |
| `sonner` | `^2.0.7` | Toast notification system |
| `cmdk` | `^1.1.1` | Command palette (⌘K) component |
| `vaul` | `^1.1.2` | Drawer/sheet component |
| `react-day-picker` | `^9.13.0` | Calendar date picker |
| `embla-carousel-react` | `^8.6.0` | Carousel component |
| `react-resizable-panels` | `^4.2.2` | Resizable split panels |
| `input-otp` | `^1.4.2` | OTP input component |
| `@hugeicons/react` | `^1.1.4` | Icon library |
| `@hugeicons/core-free-icons` | `^3.1.1` | Free icon pack |
| `@fontsource-variable/inter` | `^5.2.8` | Self-hosted Inter variable font |

### Data & Utilities

| Library | Version | Purpose |
|---------|---------|---------|
| `zod` | `^4.3.5` | Schema validation (form inputs, API payloads) |
| `react-hook-form` | `^7.70.0` | Performant form state management |
| `@hookform/resolvers` | `^5.2.2` | Zod ↔ react-hook-form bridge |
| `date-fns` | `^4.1.0` | Date formatting & manipulation |
| `jspdf` | `^4.2.0` | Client-side PDF generation |
| `jspdf-autotable` | `^5.0.7` | Table layout plugin for jsPDF |
| `@supabase/supabase-js` | `^2.39.3` | Supabase client (file storage for vendor documents) |
| `next-themes` | `^0.4.6` | Dark/light/system theme provider |
| `class-variance-authority` | `^0.7.1` | Variant-based component styling |
| `clsx` | `^2.1.1` | Conditional className builder |
| `tailwind-merge` | `^3.4.0` | Tailwind class deduplication |
| `tw-animate-css` | `^1.4.0` | Animation utilities for Tailwind |

### Dev Dependencies

| Library | Version | Purpose |
|---------|---------|---------|
| `@vitejs/plugin-react` | `^5.1.1` | React Fast Refresh for Vite |
| `babel-plugin-react-compiler` | `^1.0.0` | React Compiler (auto-memoization) |
| `eslint` | `^9.39.1` | Linting |
| `eslint-plugin-react-hooks` | `^7.0.1` | Hooks rules enforcement |
| `eslint-plugin-react-refresh` | `^0.4.24` | Fast Refresh boundary validation |
| `globals` | `^16.5.0` | Global variable definitions for ESLint |
| `@types/react` | `^19.2.5` | TypeScript types for editor IntelliSense |
| `@types/react-dom` | `^19.2.3` | TypeScript types for ReactDOM |

---

## Prerequisites

| Tool | Version |
|------|---------|
| **Node.js** | v18.0+ (LTS recommended) |
| **npm** | v9+ (ships with Node) |
| **Backend** | SupplySync backend running on `localhost:8000` |

---

## Environment Variables

Create a `.env` file in the project root:

```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_PUBLISHABLE_DEFAULT_KEY=eyJhbGciOiJI...your-anon-key
```

> These are required for vendor document upload/download via Supabase Storage.

---

## Scripts

```bash
npm install          # Install all dependencies
npm run dev          # Start Vite dev server → http://localhost:5173
npm run build        # Production build → /dist
npm run preview      # Preview production build locally
npm run lint         # Run ESLint across the project
```

### Vite Configuration Highlights

```
vite.config.js
├── react()                          # React plugin with React Compiler (babel-plugin-react-compiler)
├── tailwindcss()                    # Tailwind CSS v4 native Vite plugin
└── resolve.alias: "@" → "./src"     # Import alias: @/components/... → src/components/...
```

---

## Folder Structure

```
src/
├── main.jsx                    # App entry point — mounts BrowserRouter + route tree
├── index.css                   # Tailwind imports + CSS custom properties (light/dark themes)
│
├── assets/                     # Static assets (images, SVGs)
│
├── components/
│   ├── admin/                  # Admin-specific feature components
│   │   ├── dashboard/          # Dashboard stats, charts, report dialog
│   │   ├── inventory/          # Plant inventory cards, stock alerts
│   │   ├── orders/             # PO table, create/action dialogs
│   │   └── vendor/             # Vendor cards, create vendor form
│   │
│   ├── vendor/                 # Vendor-specific feature components
│   │   ├── dashboard/          # Vendor stats, compliance, alerts
│   │   ├── orders/             # Order table, detail view, action requests
│   │   └── profile/            # Profile form, document management
│   │
│   ├── auth/                   # Authentication components
│   │   ├── AuthGuard.jsx       # Route protection (role-based redirect)
│   │   ├── LoginCard.jsx       # Login form with validation
│   │   ├── ForgotPasswordDialog.jsx
│   │   └── LogoutButton.jsx
│   │
│   ├── common/                 # Shared components used across roles
│   │   ├── DataTable.jsx       # Reusable paginated data table
│   │   ├── Header.jsx          # Top navigation bar
│   │   ├── BrandLogo.jsx       # Clickable logo with role-based navigation
│   │   └── ThemeSwitcher.jsx   # Light/dark/system theme toggle
│   │
│   ├── layouts/                # Page layout wrappers
│   │   ├── RootLayout.jsx      # Auth initialization + theme provider
│   │   ├── AdminLayout.jsx     # Admin role guard + header + Outlet
│   │   └── VendorLayout.jsx    # Vendor role guard + header + Outlet
│   │
│   └── ui/                     # 53 Shadcn UI primitives (button, card, dialog, table, etc.)
│
├── constants/
│   ├── api-endpoints.js        # All REST endpoint URLs + URL-builder functions
│   ├── entities.js             # Enums: roles, statuses, units, document types
│   └── message-mapper.js       # Backend message code → user-friendly string mapper
│
├── hooks/                      # Custom React hooks (placeholder for future use)
│
├── lib/
│   ├── utils.js                # cn() class merger, credential generator, clipboard
│   ├── date-time.js            # UTC ↔ local timezone conversion utilities
│   ├── supabase.js             # Supabase client initialization
│   ├── report-generator.js     # PDF report builder (orders, vendors, inventory, metrics)
│   └── schemas/                # Zod validation schemas
│       ├── login.schema.js
│       ├── create-vendor.schema.js
│       ├── update-vendor.schema.js
│       └── create-order.schema.js
│
├── pages/                      # Top-level route components (1 per route)
│   ├── LandingPage.jsx
│   ├── AdminDashboardPage.jsx
│   ├── AdminVendorListingPage.jsx
│   ├── AdminVendorDetailsPage.jsx
│   ├── AdminInventoryPage.jsx
│   ├── AdminPurchaseOrdersPage.jsx
│   ├── VendorDashboardPage.jsx
│   ├── VendorPurchaseOrdersPage.jsx
│   ├── VendorOrderDetailsPage.jsx
│   ├── VendorProductsPage.jsx
│   ├── VendorProfilePage.jsx
│   ├── VendorDetailsPage.jsx
│   ├── ResetPasswordPage.jsx
│   ├── NotFoundPage.jsx
│   └── UnauthorizedPage.jsx
│
├── services/                   # Axios-based API service modules
│   ├── api.service.js          # Singleton Axios wrapper (interceptors, token, refresh)
│   ├── auth.service.js         # Login, logout, token refresh, password reset
│   ├── vendor.service.js       # Vendor CRUD
│   ├── product.service.js      # Product catalog CRUD
│   ├── inventory.service.js    # Inventory stock & consumption
│   ├── purchase-order.service.js
│   ├── purchase-order-item.service.js
│   ├── order-action.service.js # Update/cancel/return action requests
│   ├── plant.service.js        # Plant/facility management
│   ├── vendor-product.service.js
│   ├── vendor-document.service.js
│   ├── performance-metrics.service.js
│   ├── file-upload.service.js  # Supabase storage operations
│   └── user.service.js         # User account management
│
└── store/                      # Zustand global state stores
    ├── auth.store.js           # Authentication state (token, user, refresh logic)
    └── theme.store.js          # Theme preference (light/dark/system)
```

---

## Routing & Page Map

All routes are defined in `main.jsx` inside a single `<BrowserRouter>`:

```
/                                → RootLayout
├── /                            → LandingPage (public — login form + hero section)
├── /reset-password              → ResetPasswordPage (token-based password reset)
│
├── /admin                       → AdminLayout (ADMIN role guard)
│   ├── /admin                   → AdminDashboardPage (stats, charts, reports)
│   ├── /admin/manage/vendors    → AdminVendorListingPage (vendor list + create)
│   ├── /admin/manage/vendors/:vendorId → AdminVendorDetailsPage (detail + docs)
│   ├── /admin/manage/inventory  → AdminInventoryPage (plant-wise stock)
│   ├── /admin/manage/purchase-orders → AdminPurchaseOrdersPage (PO management)
│   └── /admin/*                 → NotFoundPage
│
├── /vendor                      → VendorLayout (VENDOR role guard)
│   ├── /vendor                  → VendorDashboardPage (KPIs, alerts, compliance)
│   ├── /vendor/manage/orders    → VendorPurchaseOrdersPage (order list)
│   ├── /vendor/manage/orders/:id → VendorOrderDetailsPage (single order)
│   ├── /vendor/manage/profile   → VendorProfilePage (edit profile + documents)
│   ├── /vendor/manage/products  → VendorProductsPage (product catalog CRUD)
│   └── /vendor/*                → NotFoundPage
│
├── /unauthorized                → UnauthorizedPage (403)
└── /*                           → NotFoundPage (404)
```

### Route Guard Flow

```
User visits /admin/manage/vendors
       │
       ▼
  RootLayout
  ├── Reads auth.store (token, user, role)
  ├── If no token → redirect to /
  ├── If token expired → attempt silent refresh
  │
  └── AdminLayout
      ├── Checks user.role === "ADMIN"
      ├── If not ADMIN → redirect to /unauthorized
      └── Renders <AdminVendorListingPage /> via <Outlet>
```

---

## Component Tree Breakdown

### Admin Dashboard (`/admin`)

```
AdminDashboardPage
├── StatsCard × 3 (Total vendors, pending orders, generate report)
├── GenerateReportDialog
│   └── Date range picker → PDF download (orders/vendors/inventory/metrics)
├── OrderStatusPieChart (recharts PieChart)
├── InventoryLevelChart (recharts LineChart)
├── VendorPerformanceChart (recharts BarChart)
└── QuickLinks (navigate to vendors, orders, inventory)
```

### Admin Vendor Management (`/admin/manage/vendors`)

```
AdminVendorListingPage
├── Search + Status filter
├── CreateVendorForm (dialog)
│   └── react-hook-form + Zod validation → creates user + vendor
└── VendorCard × N
    └── Click → navigates to AdminVendorDetailsPage

AdminVendorDetailsPage
├── Vendor info (name, status, contact, address)
├── Status actions (Approve / Suspend / Deactivate)
└── Compliance documents table (view/verify uploaded docs)
```

### Admin Purchase Orders (`/admin/manage/purchase-orders`)

```
AdminPurchaseOrdersPage
├── CreatePurchaseOrderDialog
│   └── Multi-step form: select vendor → add items → review → submit
├── PurchaseOrderTable
│   ├── Sortable columns, status badges
│   ├── Row click → ViewPurchaseOrderDialog (read-only detail)
│   └── Action menu → OrderActionDialog (cancel/return/restock)
└── Pagination controls
```

### Admin Inventory (`/admin/manage/inventory`)

```
AdminInventoryPage
├── Plant selector tabs
├── LowStockAlerts (products below reorder level)
└── PlantInventorySection
    └── PlantInventoryCard × N (product stock per plant)
        └── Restock / Consume actions
```

### Vendor Dashboard (`/vendor`)

```
VendorDashboardPage
├── VendorStatCard × 4 (total orders, pending, delivered, on-time %)
├── PerformanceMetrics (delivery rate, quality score)
├── ComplianceStatus (document verification progress)
├── PendingActionsAlert (admin requests needing response)
├── QuickLinks
└── RecentOrders (last 5 orders)
```

### Vendor Order Management (`/vendor/manage/orders`)

```
VendorPurchaseOrdersPage
├── Filter/search controls
├── VendorOrderTable
│   ├── Status badges, pending action indicators
│   └── Row click → VendorOrderDetailsPage
└── Pagination

VendorOrderDetailsPage
├── Order header (ID, status, dates, vendor info)
├── Line items table (product, qty, unit price, total)
├── Status update form (confirm → ship → deliver)
├── Delivery date editor
├── PendingActionRequests (approve/reject admin's cancel/return)
└── Quality rating display
```

### Vendor Products (`/vendor/manage/products`)

```
VendorProductsPage
├── Search bar
├── Add Product dialog (form with Zod validation)
├── Product cards grid
│   └── Edit / Delete actions per card
└── Empty state when no products
```

### Vendor Profile (`/vendor/manage/profile`)

```
VendorProfilePage
├── VendorDetailsForm (business name, contact, address, etc.)
├── ComplianceDocumentsTable
│   ├── Document rows (type, status, uploaded date)
│   ├── Upload Document dialog → Supabase storage
│   └── DeleteDocumentDialog (confirmation)
└── Save button
```

---

## State Management (Zustand)

### `auth.store.js`

The primary global store. Uses `persist` middleware with `localStorage` for session survival across page reloads.

| State | Type | Description |
|-------|------|-------------|
| `isAuthenticated` | `boolean` | Whether user is logged in |
| `token` | `string` | Current JWT access token |
| `user` | `object` | `{ id, email, role, vendorId }` |
| `loading` | `boolean` | Auth operation in progress |
| `error` | `string` | Last auth error message |

| Action | Description |
|--------|-------------|
| `setAuth(isAuthenticated, token, user, refreshToken)` | Set full auth state after login |
| `clearAuth()` | Wipe auth state + localStorage (logout) |
| `login(email, password)` | Call auth API → set tokens + user |
| `logout()` | Call logout API → clear state |
| `refreshAuth()` | Silent token refresh (singleton Promise pattern) |
| `forgotPassword(email)` | Request password reset email |
| `resetPassword(token, password)` | Submit new password with reset token |

**Singleton Refresh Pattern:**
```
refreshAuth() is wrapped in a module-level `refreshPromise` variable.
If a refresh is already in-flight, all subsequent callers receive the same Promise.
This prevents multiple concurrent 401 responses from spawning duplicate refresh calls.
```

### `theme.store.js`

| State | Type | Description |
|-------|------|-------------|
| `theme` | `"light" \| "dark" \| "system"` | Current theme preference |

| Action | Description |
|--------|-------------|
| `setTheme(theme)` | Update theme preference |

---

## Service Layer & API Flow

All backend communication follows this pattern:

```
Component (useEffect / event handler)
    │
    ▼
Service Module (e.g., vendor.service.js)
    │  - Constructs URL from api-endpoints.js
    │  - Calls apiService.sendRequest(method, url, data)
    │
    ▼
ApiService (api.service.js — singleton Axios wrapper)
    │
    ├── Request Interceptor
    │   └── Attaches "Authorization: Bearer <token>" header
    │       (skips /auth/refresh to avoid stale token loop)
    │
    ├── HTTP Request → API Gateway (localhost:8000)
    │
    └── Response Interceptor
        ├── 2xx → return response.data
        └── 401 → trigger refreshAuth() → retry original request
            └── If refresh fails → clearAuth() → redirect to /
```

### Service Modules

| Service File | Backend Service | Key Operations |
|-------------|-----------------|----------------|
| `auth.service.js` | auth-service:8081 | login, logout, refresh, forgot/reset password |
| `user.service.js` | auth-service:8081 | CRUD for user accounts |
| `vendor.service.js` | vendor-service:8082 | Vendor CRUD, status management |
| `vendor-document.service.js` | vendor-service:8082 | Compliance document CRUD |
| `vendor-product.service.js` | vendor-service:8082 | Vendor-product catalog linking |
| `inventory.service.js` | inventory-service:8083 | Stock queries, restock, consume |
| `purchase-order.service.js` | order-service:8084 | PO CRUD with pagination/filtering |
| `purchase-order-item.service.js` | order-service:8084 | PO line items |
| `order-action.service.js` | order-service:8084 | Cancel/return/update action requests |
| `product.service.js` | product-service:8085 | Product master CRUD |
| `plant.service.js` | product-service:8085 | Plant/facility management |
| `performance-metrics.service.js` | analytics-service:8086 | Vendor performance scores |
| `file-upload.service.js` | Supabase Storage | Upload/download/delete documents |

---

## Authentication Flow

### Login

```
LandingPage → LoginCard
    │
    ▼
authStore.login(email, password)
    │
    ▼
POST /api/auth/login → { token, refreshToken, user }
    │
    ▼
setAuth() → persist to localStorage
    │
    ▼
React Router redirect → /admin or /vendor (based on user.role)
```

### Silent Token Refresh (401 Interceptor)

```
Any API call returns 401
    │
    ▼
Response interceptor catches it
    │
    ├── Is this /auth/refresh? → YES → clearAuth() + redirect (prevent loop)
    │
    └── NO → call authStore.refreshAuth()
             │
             ├── refreshPromise already exists? → await same Promise
             │
             └── POST /api/auth/refresh { refreshToken }
                      │
                      ├── Success → update token in store + localStorage
                      │              retry original failed request
                      │
                      └── Failure → clearAuth() → redirect to /
```

### Password Reset

```
LoginCard → ForgotPasswordDialog → POST /api/auth/forgot-password { email }
    │
    ▼ (email sent with reset link containing token)
    
User clicks link → /reset-password?token=xxx
    │
    ▼
ResetPasswordPage → POST /api/auth/reset-password { token, newPassword }
```

---

## Key Libraries Explained

### Shadcn UI (`src/components/ui/`)

Not an npm package — Shadcn generates **source code** directly into `src/components/ui/`. These 53 components are locally owned and customizable. They are built on **Radix UI** primitives with **Tailwind CSS** styling via `class-variance-authority` (CVA).

Key components used: `Button`, `Card`, `Dialog`, `Table`, `Select`, `Badge`, `DropdownMenu`, `Tabs`, `Tooltip`, `Calendar`, `Popover`, `Sheet`, `Sonner` (toasts).

### React Hook Form + Zod

Forms use `react-hook-form` for state + `zod` for validation, connected via `@hookform/resolvers`:

```
Zod Schema (lib/schemas/*.js)
    ↓
useForm({ resolver: zodResolver(schema) })
    ↓
<form onSubmit={handleSubmit(onSubmit)}>
    ↓
Validation errors auto-mapped to form fields
```

### PDF Report Generation (`lib/report-generator.js`)

The admin can generate PDF reports covering:
- **Purchase Orders** — table of all orders with status, dates, totals
- **Vendor Performance** — delivery rates, quality scores per vendor
- **Inventory Status** — stock levels, reorder alerts per plant
- **Overall Metrics** — combined summary report

Uses `jspdf` for document creation and `jspdf-autotable` (imported as `autoTable`) for table layouts.

### Supabase Integration

Used exclusively for **file storage** (vendor compliance documents like ISO certificates, GST, PAN, etc.). The Supabase client is initialized in `lib/supabase.js` and consumed by `file-upload.service.js`.

---

## Conventions & Standards

### File Naming
- **Pages:** `PascalCase` with `Page` suffix → `AdminDashboardPage.jsx`
- **Components:** `PascalCase` → `StatsCard.jsx`, `CreateVendorForm.jsx`
- **Services:** `kebab-case` with `.service.js` suffix → `vendor.service.js`
- **Stores:** `kebab-case` with `.store.js` suffix → `auth.store.js`
- **Schemas:** `kebab-case` with `.schema.js` suffix → `create-vendor.schema.js`
- **Utilities:** `kebab-case` → `date-time.js`, `report-generator.js`

### Import Alias
All imports use `@/` alias mapped to `src/`:
```javascript
import useAuthStore from "@/store/auth.store";
import { VENDOR_ENDPOINT } from "@/constants/api-endpoints";
```

### Component Pattern
- Functional components only (no class components)
- React Compiler enabled (auto-memoization via `babel-plugin-react-compiler`)
- Forms use `react-hook-form` + Zod schema resolver
- API calls in `useEffect` or event handlers, never directly in render

### Styling
- Tailwind CSS utility classes exclusively (no custom CSS beyond `index.css` theme vars)
- Dark mode via `next-themes` ThemeProvider + CSS custom properties
- `cn()` utility (from `lib/utils.js`) for conditional class merging

### API Communication
- Every backend entity has a dedicated service file in `src/services/`
- All HTTP calls go through `ApiService.sendRequest()` for consistent interceptor handling
- Endpoint URLs centralized in `constants/api-endpoints.js`
- Backend response messages mapped via `constants/message-mapper.js` for user-friendly toasts

---

## Known Unused Dependencies

Identified via `depcheck`:

| Package | Status | Notes |
|---------|--------|-------|
| `@fontsource-variable/inter` | Unused in JS imports | Imported via CSS (`@import`) — **keep** |
| `shadcn` | CLI-only | Used for `npx shadcn add` commands — **keep** |
| `tw-animate-css` | Unused in JS imports | Imported via CSS (`@import`) — **keep** |
| `@types/react-dom` | Dev types | IntelliSense only — **keep** |
| `babel-plugin-react-compiler` | Config-only | Referenced in `vite.config.js` — **keep** |

> All flagged packages are actually used via CSS imports or tooling config. No true dead dependencies.

---

## Quick-Start for New Developers

### 1. Clone & Install

```bash
git clone <repo-url>
cd supply-sync
npm install
```

### 2. Configure Environment

Copy `.env.example` to `.env` and fill in Supabase credentials.

### 3. Start Backend

Ensure the backend services are running (see `supply-sync-backend/README.md`):
```bash
cd ../supply-sync-backend
./start.ps1
```

### 4. Start Frontend

```bash
npm run dev
# App runs on http://localhost:5173
```

### 5. Login Credentials

| Role | Email | Password |
|------|-------|----------|
| Admin | *(seeded via backend `seed-data.sql`)* | *(check seed file)* |
| Vendor | *(created via Admin dashboard)* | *(auto-generated, shown on creation)* |

### 6. Key Files to Read First

1. `src/main.jsx` — Understand the full route tree
2. `src/services/api.service.js` — Understand how all HTTP calls work
3. `src/store/auth.store.js` — Understand auth state and token refresh
4. `src/constants/api-endpoints.js` — See all backend endpoints
5. `src/constants/entities.js` — See all domain enums (statuses, roles, etc.)

### 7. Adding a New Page

1. Create page component in `src/pages/NewPage.jsx`
2. Add route in `src/main.jsx` under appropriate layout
3. Create service file in `src/services/` if new API endpoints needed
4. Add endpoint URLs to `src/constants/api-endpoints.js`
5. Add Zod schema to `src/lib/schemas/` for form validation

### 8. Adding a Shadcn Component

```bash
npx shadcn@latest add <component-name>
# Component source code generated in src/components/ui/
```

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    Browser (React SPA)                   │
│                                                         │
│  ┌─────────┐  ┌──────────┐  ┌───────────┐  ┌────────┐ │
│  │  Pages   │→│Components │→│  Services  │→│  Store  │ │
│  │ (routes) │  │ (UI tree) │  │ (API calls)│  │(Zustand)│ │
│  └─────────┘  └──────────┘  └─────┬─────┘  └────────┘ │
│                                    │                     │
│                              Axios + Interceptors        │
│                                    │                     │
└────────────────────────────────────┼─────────────────────┘
                                     │ HTTP (JSON)
                                     ▼
                          ┌──────────────────┐
                          │   API Gateway    │
                          │  localhost:8000   │
                          └────────┬─────────┘
                                   │
            ┌──────────┬───────────┼───────────┬───────────┐
            ▼          ▼           ▼           ▼           ▼
       auth:8081  vendor:8082  inventory:8083 order:8084 product:8085
                                                         analytics:8086
```
# Supply-Sync
# Supply-Sync
