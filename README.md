# A3 Fitness

Full-stack booking platform for an independent personal trainer: scheduling, payments, and an admin dashboard, without a third-party booking product.

This is the **earlier iteration** of the system that later became [AIO Fitness](https://github.com/bhaddish123/aio-fitness). If you want the live, maintained product, start there. A user-flow diagram is in the repo as `User_flow_pic.drawio (1).pdf`.

---

## Tech stack

- **Frontend:** React 18, Vite, React Router v6, axios, date-fns, react-big-calendar, react-easy-crop, Tailwind CSS (as documented in the original README; the frontend is a Vite React app)
- **Backend:** Java 21, Spring Boot 3.5, Spring Security, JPA
- **Data & payments:** PostgreSQL, Stripe, email via Gmail SMTP
- **Auth:** JWT admin login (`ADMIN_USERNAME` / `ADMIN_PASSWORD`, `JWT_SECRET`)

---

## Booking flow

1. Client selects a session type and an available calendar slot.
2. The backend reserves the slot (15-minute hold in this codebase's documentation) and creates a Stripe Checkout session.
3. Payment completes → Stripe webhook confirms the booking.
4. A confirmation email is sent; reminders are scheduled.

Availability comes from recurring weekly rules stored in the database; slots are computed dynamically (next 30 days). Admins can block individual slots or add one-off slots with schedule overrides. A cleanup scheduler releases unpaid reservations. The webhook handler checks existing booking status before confirming, to avoid duplicate confirmations.

---

## Project structure

```
a3fit/
├── frontend/                 # React + Vite
└── backend/a3fit-backend/    # Spring Boot
    └── src/main/java/...
        ├── controller/
        ├── service/          # business logic, email, scheduling
        ├── model/            # Booking, SessionType, AvailabilityRule, ScheduleOverride
        └── repository/
```

---

## Running locally

**Prerequisites:** Java 21, Node 18+, PostgreSQL, Stripe CLI (for webhook testing).

### Backend

```bash
export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/a3fit_backend
export SPRING_DATASOURCE_USERNAME=postgres
export SPRING_DATASOURCE_PASSWORD=your_db_password
export JWT_SECRET=your_jwt_secret
export ADMIN_USERNAME=admin
export ADMIN_PASSWORD=your_admin_password
export MAIL_USERNAME=your_gmail@gmail.com
export MAIL_PASSWORD=your_gmail_app_password
export STRIPE_API_KEY=sk_test_...
export STRIPE_WEBHOOK_SECRET=whsec_...

cd backend/a3fit-backend
./mvnw spring-boot:run
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

App: `http://localhost:5173` · API: `http://localhost:8080`.

---

## See also

[aio-fitness](https://github.com/bhaddish123/aio-fitness) — the production version of this design (Google Calendar sync, intake forms, site-content CMS, and a live deployment).
