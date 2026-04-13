# CrewHub — Service Marketplace Implementation Plan

A full-stack service marketplace connecting customers with verified professionals (electricians, plumbers, carpenters, etc.). Customers search & book; workers manage jobs & earnings; admins oversee the platform.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | **Vite + React** (component-based SPA) |
| Styling | **Vanilla CSS** with CSS custom properties design system |
| Routing | **React Router v6** |
| HTTP Client | **Axios** (service layer) |
| Backend | **Flask** (Python) on `http://localhost:5000` |
| Database | **MongoDB** via **PyMongo / Flask-PyMongo** |
| Auth | **JWT** (Flask-JWT-Extended) |
| File Uploads | **Cloudinary** (Python SDK) |
| Dev Server | Vite dev server proxied to Flask |

---

## User Review Required

> [!IMPORTANT]
> **React vs Vanilla JS** — The plan uses **Vite + React** for the component-based architecture. If you prefer vanilla JS or another framework, let me know before I begin.

> [!IMPORTANT]
> **MongoDB Setup** — The plan assumes MongoDB is running locally on `mongodb://localhost:27017`. Confirm if you're using MongoDB Atlas or a different connection string.

> [!IMPORTANT]
> **Cloudinary Credentials** — You'll need to provide `CLOUD_NAME`, `API_KEY`, and `API_SECRET` in a `.env` file. I'll create a `.env.example` template.

> [!WARNING]
> **Scope** — This is a large application. The initial build will include all pages and components with working API integration. Mock/seed data will be provided so the app is functional even before the backend is fully wired.

---

## Folder Structure

```
e:\Dev\Web-Projects\CrewHub\
├── frontend/                    # Vite + React SPA
│   ├── public/
│   │   └── favicon.svg
│   ├── src/
│   │   ├── assets/              # Static images, icons
│   │   ├── components/          # Reusable UI components
│   │   │   ├── Navbar/
│   │   │   │   ├── Navbar.jsx
│   │   │   │   └── Navbar.css
│   │   │   ├── SearchBar/
│   │   │   ├── ProfessionalCard/
│   │   │   ├── StatCard/
│   │   │   ├── BookingModal/
│   │   │   ├── StatusBadge/
│   │   │   ├── FeatureCard/
│   │   │   ├── CategoryPill/
│   │   │   ├── InvoiceCard/
│   │   │   ├── DataTable/
│   │   │   └── Footer/
│   │   ├── pages/
│   │   │   ├── Home/
│   │   │   ├── FindPros/
│   │   │   ├── WorkerDashboard/
│   │   │   ├── CustomerDashboard/
│   │   │   ├── AdminPanel/
│   │   │   ├── Login/
│   │   │   └── Signup/
│   │   ├── services/            # API service layer (axios)
│   │   │   ├── api.js           # Axios instance + interceptors
│   │   │   ├── authService.js
│   │   │   ├── bookingService.js
│   │   │   ├── workerService.js
│   │   │   ├── customerService.js
│   │   │   └── adminService.js
│   │   ├── context/             # React Context for auth state
│   │   │   └── AuthContext.jsx
│   │   ├── hooks/               # Custom hooks
│   │   │   └── useAuth.js
│   │   ├── utils/               # Helpers, constants
│   │   │   ├── constants.js
│   │   │   └── helpers.js
│   │   ├── App.jsx
│   │   ├── App.css
│   │   ├── index.css            # Global design system
│   │   └── main.jsx
│   ├── index.html
│   ├── vite.config.js
│   └── package.json
│
├── backend/                     # Flask REST API
│   ├── app/
│   │   ├── __init__.py          # Flask app factory
│   │   ├── config.py            # Configuration
│   │   ├── models/
│   │   │   ├── user.py
│   │   │   ├── booking.py
│   │   │   ├── worker_profile.py
│   │   │   └── invoice.py
│   │   ├── routes/
│   │   │   ├── auth.py
│   │   │   ├── customers.py
│   │   │   ├── workers.py
│   │   │   ├── bookings.py
│   │   │   ├── admin.py
│   │   │   └── upload.py
│   │   ├── middleware/
│   │   │   └── auth_middleware.py
│   │   ├── services/
│   │   │   ├── auth_service.py
│   │   │   ├── booking_service.py
│   │   │   ├── worker_service.py
│   │   │   └── cloudinary_service.py
│   │   └── utils/
│   │       ├── helpers.py
│   │       └── seed.py          # Seed data script
│   ├── requirements.txt
│   ├── .env.example
│   └── run.py
│
└── README.md
```

---

## Proposed Changes

### Design System — Global CSS

#### [NEW] `index.css`
- CSS custom properties: teal primary (`#2a9d8f`), dark text, light backgrounds
- Typography: **Inter** from Google Fonts (matches the clean UI in screenshots)
- Spacing scale, border-radius tokens, shadow levels
- Utility classes for badges, status colors, layout grids

---

### Component Layer (Reusable)

#### [NEW] `Navbar/Navbar.jsx` + `Navbar.css`
- Logo ("C" circle + "CrewHub" text), nav links (Home, Find Pros, Worker Panel, Admin)
- Active link highlighting (teal text + underline)
- Right side: notification bell icon, profile icon, "Get Started" CTA button
- Responsive hamburger menu for mobile
- Conditionally shows links based on user role

#### [NEW] `SearchBar/SearchBar.jsx` + `SearchBar.css`
- Three input fields: Service (with search icon), City (with location icon), Pincode (with hash icon)
- Teal "Search" button
- Glassmorphic container with subtle border and shadow
- `onSearch(filters)` callback

#### [NEW] `CategoryPill/CategoryPill.jsx` + `CategoryPill.css`
- Rounded pill buttons: Electrician, Plumber, Carpenter, Painter, Cleaner, AC Repair
- Click selects/deselects, active state with teal border
- Horizontal scrollable on mobile

#### [NEW] `FeatureCard/FeatureCard.jsx` + `FeatureCard.css`
- Icon in teal circle, bold title, subtitle text
- Used for: Verified Pros, Fast Booking, Top Rated, Instant Match

#### [NEW] `ProfessionalCard/ProfessionalCard.jsx` + `ProfessionalCard.css`
- Worker photo (top), name, title, rate (₹/hr in teal)
- Star rating + review count, location pin + city
- Skill tags (pill badges)
- "Book Now" CTA button (full-width, teal)

#### [NEW] `StatCard/StatCard.jsx` + `StatCard.css`
- Label, large value, optional trend text ("+12% this month" in teal)
- Icon in top-right corner
- Used on Worker Dashboard (Earnings, Jobs, Rating, Upcoming)

#### [NEW] `DataTable/DataTable.jsx` + `DataTable.css`
- Reusable table with teal header labels
- Status column uses colored badges (Accepted=green, Pending=orange, Completed=teal)
- Used for Recent Jobs (Worker), Bookings (Customer), User Management (Admin)

#### [NEW] `BookingModal/BookingModal.jsx` + `BookingModal.css`
- Modal overlay with form: select date, time slot, notes
- "Confirm Booking" button
- Triggered from ProfessionalCard "Book Now"

#### [NEW] `StatusBadge/StatusBadge.jsx` + `StatusBadge.css`
- Colored text badge: Accepted (green), Pending (orange), Completed (teal), Rejected (red)

#### [NEW] `InvoiceCard/InvoiceCard.jsx` + `InvoiceCard.css`
- "Latest Invoice" label with download icon link

#### [NEW] `Footer/Footer.jsx` + `Footer.css`
- Simple footer with copyright, links

---

### Pages

#### [NEW] `Home/Home.jsx` + `Home.css`
**Sections (matching the provided screenshots):**
1. **Hero** — "Trusted by 10,000+ customers" badge → "Find Trusted Local Professionals" heading (with "Professionals" in teal) → subtitle → SearchBar → CategoryPills
2. **Features Grid** — 2×2 grid of FeatureCards (Verified Pros, Fast Booking, Top Rated, Instant Match)
3. **Featured Professionals** — "Featured Professionals" heading + "View All →" link → horizontal scroll of ProfessionalCards
4. **Footer**

#### [NEW] `FindPros/FindPros.jsx` + `FindPros.css`
- SearchBar at top (sticky)
- Filter sidebar: category checkboxes, rating range, price range
- Results grid of ProfessionalCards
- Pagination
- Click card → worker profile detail (or modal)

#### [NEW] `CustomerDashboard/CustomerDashboard.jsx` + `CustomerDashboard.css`
- Welcome header with user name
- Active bookings list (cards with status badges)
- Booking history table
- Quick re-book from history

#### [NEW] `WorkerDashboard/WorkerDashboard.jsx` + `WorkerDashboard.css`
**Matching the provided screenshot:**
- 4 StatCards: Total Earnings (₹1,24,500), Jobs Completed (48), Avg. Rating (4.9), Upcoming (3)
- "Recent Jobs" DataTable: Client, Service, Date, Amount, Status
- "Latest Invoice" card with Download link
- Accept/Reject action buttons on pending bookings

#### [NEW] `AdminPanel/AdminPanel.jsx` + `AdminPanel.css`
- Tab navigation: Users, Workers, Bookings, Logs
- **Users tab**: DataTable with name, email, role, status, Ban/Unban toggle
- **Workers tab**: Pending approvals list with Approve/Reject buttons
- **Bookings tab**: All bookings table with filters
- **Logs tab**: System activity log (scrollable list)

#### [NEW] `Login/Login.jsx` + `Login.css`
- Clean centered form: email, password, "Login" button
- "Don't have an account? Sign up" link
- Role indicator (Customer/Worker)

#### [NEW] `Signup/Signup.jsx` + `Signup.css`
- Form: name, email, phone, password, role selection (Customer/Worker)
- Worker-specific fields: category, skills, hourly rate, profile photo upload
- "Create Account" button

---

### API Service Layer (Frontend)

#### [NEW] `services/api.js`
- Axios instance with `baseURL: http://localhost:5000/api`
- Request interceptor: attach JWT from localStorage
- Response interceptor: handle 401 → redirect to login

#### [NEW] `services/authService.js`
- `login(email, password)` → `POST /api/auth/login`
- `signup(userData)` → `POST /api/auth/signup`
- `getProfile()` → `GET /api/auth/profile`
- `logout()` → clear token

#### [NEW] `services/bookingService.js`
- `createBooking(data)` → `POST /api/bookings`
- `getMyBookings()` → `GET /api/bookings/my`
- `updateBookingStatus(id, status)` → `PATCH /api/bookings/:id`

#### [NEW] `services/workerService.js`
- `searchWorkers(filters)` → `GET /api/workers/search`
- `getWorkerProfile(id)` → `GET /api/workers/:id`
- `getWorkerDashboard()` → `GET /api/workers/dashboard`
- `getEarnings()` → `GET /api/workers/earnings`
- `generateInvoice(bookingId)` → `POST /api/workers/invoice`

#### [NEW] `services/adminService.js`
- `getUsers()` → `GET /api/admin/users`
- `banUser(id)` / `unbanUser(id)` → `PATCH /api/admin/users/:id`
- `getPendingWorkers()` → `GET /api/admin/workers/pending`
- `approveWorker(id)` / `rejectWorker(id)` → `PATCH /api/admin/workers/:id`
- `getLogs()` → `GET /api/admin/logs`

---

### Auth Context

#### [NEW] `context/AuthContext.jsx`
- React Context providing: `user`, `token`, `login()`, `signup()`, `logout()`
- Persists token in localStorage
- Provides role-based access helpers: `isCustomer`, `isWorker`, `isAdmin`

---

### Backend — Flask API

#### [NEW] `app/__init__.py`
- Flask app factory with CORS, JWT, PyMongo initialization
- Blueprint registration for all route modules

#### [NEW] `app/config.py`
- `MONGO_URI`, `JWT_SECRET_KEY`, Cloudinary credentials from env

#### [NEW] `app/models/user.py`
- User schema: name, email, password_hash, phone, role, status, created_at
- Helper methods: `to_dict()`, password hashing

#### [NEW] `app/models/worker_profile.py`
- WorkerProfile: user_id, category, skills[], hourly_rate, rating, reviews_count, city, pincode, photo_url, verified, bio

#### [NEW] `app/models/booking.py`
- Booking: customer_id, worker_id, service, date, time_slot, status, amount, notes, created_at

#### [NEW] `app/models/invoice.py`
- Invoice: booking_id, worker_id, amount, tax, total, generated_at, pdf_url

#### [NEW] `app/routes/auth.py`
- `POST /api/auth/signup` — register with hashed password, return JWT
- `POST /api/auth/login` — verify credentials, return JWT + user
- `GET /api/auth/profile` — get current user (JWT required)

#### [NEW] `app/routes/workers.py`
- `GET /api/workers/search` — filter by category, city, pincode
- `GET /api/workers/:id` — worker profile detail
- `GET /api/workers/dashboard` — earnings, jobs, rating (worker only)
- `POST /api/workers/invoice` — generate invoice PDF

#### [NEW] `app/routes/bookings.py`
- `POST /api/bookings` — create booking (customer only)
- `GET /api/bookings/my` — get user's bookings
- `PATCH /api/bookings/:id` — update status (accept/reject/complete)

#### [NEW] `app/routes/admin.py`
- `GET /api/admin/users` — list all users
- `PATCH /api/admin/users/:id` — ban/unban
- `GET /api/admin/workers/pending` — pending worker approvals
- `PATCH /api/admin/workers/:id` — approve/reject
- `GET /api/admin/logs` — system logs

#### [NEW] `app/routes/upload.py`
- `POST /api/upload` — upload file to Cloudinary, return URL

#### [NEW] `app/middleware/auth_middleware.py`
- JWT verification decorator
- Role-checking decorator (`require_role('admin')`)

#### [NEW] `app/services/cloudinary_service.py`
- Upload image/video to Cloudinary
- Return secure URL

#### [NEW] `app/utils/seed.py`
- Seed script: creates sample users (1 admin, 3 workers, 3 customers), sample bookings, worker profiles

---

## Open Questions

> [!IMPORTANT]
> **MongoDB Connection** — Are you using local MongoDB (`mongodb://localhost:27017`) or MongoDB Atlas? If Atlas, please share the connection string format you'd like to use.

> [!NOTE]
> **Image Assets** — I'll generate placeholder professional images using the image generation tool for the worker cards. The final app will load images from Cloudinary.

---

## Verification Plan

### Automated Tests
1. `npm run dev` — Verify frontend starts without errors
2. `python run.py` — Verify Flask backend starts
3. Navigate all pages in browser: Home, Find Pros, Worker Dashboard, Customer Dashboard, Admin Panel, Login, Signup
4. Test search flow: enter service + city → see filtered results
5. Test booking flow: click Book Now → fill modal → confirm
6. Test worker dashboard: view stats, accept/reject booking
7. Test admin panel: approve worker, ban user

### Manual Verification
- Visual comparison against provided UI screenshots
- Browser recording of key user flows
- Responsive design check at mobile/tablet breakpoints
