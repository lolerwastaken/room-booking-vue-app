# CUreserve — Campus Room Booking & Reservation System

**CUreserve** is a responsive web application built with **Vue 3** and **Vite** for browsing campus meeting locations and reserving shared spaces across Chulalongkorn University. It uses **Supabase (PostgreSQL)** for persistent relational data storage and **Make.com** for automated activity logging via database webhooks.

---

## Key Features

- **Interactive Dashboard:** View active and upcoming room reservations with automatic thumbnail previews, date/time slots, booking statuses, and agendas.
- **Mobile-First Responsive Design:** Adapts cleanly across desktop, tablet, and mobile viewports—converting wide data tables into stacked mobile cards and optimizing modal forms for touch devices.
- **Space & Facility Catalog:** Browse available campus locations (*Engineering Innovation Center*, *Central Academic Auditorium Complex*, and *Library & Learning Commons*) along with room lists and facility descriptions.
- **Reservation Modal (`+ Reserve`):** Submit new room bookings with dynamic room filtering per location, optional image attachment previews, and a live 120-character agenda counter.
- **Automated Backend Audit Logging:** Inserting a reservation into the primary `bookings` table triggers a Supabase Database Webhook to Make.com, which automatically records a linked entry in `booking_logs`.

---

## Tech Stack

- **Frontend:** Vue 3 (Composition API `<script setup>`), Vite, CSS3 (Responsive Grid & Flexbox)
- **Database & API:** Supabase (`@supabase/supabase-js`, PostgreSQL)
- **Automation:** Make.com (Custom Webhooks + PostgreSQL Integration)

---

## Project Structure

```text
room-booking-vue-app/
├── public/
│   └── favicon.ico               # Application favicon
├── src/
│   ├── assets/
│   │   ├── 7080840.png           # CUreserve brand logo
│   │   ├── defaultImage.png      # Fallback thumbnail when no image is uploaded
│   │   └── favicon.ico           # Favicon
│   ├── components/
│   │   ├── BookingForm.vue       # Reservation modal form with image upload & validation
│   │   ├── BookingList.vue       # Responsive table/card view of scheduled bookings
│   │   └── MeetingLocations.vue  # Campus facilities & room catalog grid
│   ├── lib/
│   │   └── supabaseClient.js     # Supabase JS client configuration
│   ├── App.vue                   # Main layout, navigation, and state management
│   └── main.js                   # Vue application entry point
├── .env                          # Environment variables (not committed to Git)
├── index.html                    # HTML entry point
├── package.json                  # Project dependencies and scripts
└── vite.config.js                # Vite build configuration
```

---

## Local Setup Guide

Follow these steps to run **CUreserve** locally on your machine.

### Prerequisites

- **Node.js** (v18.0 or higher recommended) and **npm**
- A free [Supabase](https://supabase.com/) account
- A free [Make.com](https://www.make.com/) account (for automated activity logging)

---

### Step 1: Clone & Install Dependencies

1. Open your terminal and navigate to the project directory:
   ```bash
   cd room-booking-vue-app
   ```
2. Install the required npm packages (including `@supabase/supabase-js`):
   ```bash
   npm install
   ```

---

### Step 2: Set Up the Supabase Database

1. Sign in to [Supabase](https://supabase.com/) and create a new project (save your **Database Password** for Step 4).
2. In the left sidebar, open **SQL Editor** $\rightarrow$ **New query**.
3. Paste and run the following SQL script to create the primary table (`bookings`), the supporting table (`booking_logs`), and a sample record:

   ```sql
   -- 1. Primary Table: Stores main room reservations
   CREATE TABLE bookings (
     id SERIAL PRIMARY KEY,
     location TEXT NOT NULL,
     room_name TEXT NOT NULL,
     booked_by TEXT NOT NULL,
     booking_date DATE NOT NULL,
     start_time TIME NOT NULL,
     end_time TIME NOT NULL,
     purpose TEXT,
     image_url TEXT,
     status TEXT DEFAULT 'confirmed',
     created_at TIMESTAMPTZ DEFAULT NOW()
   );

   -- 2. Supporting Table: Linked activity logs populated via Make.com
   CREATE TABLE booking_logs (
     log_id SERIAL PRIMARY KEY,
     booking_id INT REFERENCES bookings(id) ON DELETE CASCADE,
     event_type TEXT NOT NULL,
     details TEXT,
     logged_at TIMESTAMPTZ DEFAULT NOW()
   );

   -- 3. Sample seed record
   INSERT INTO bookings (location, room_name, booked_by, booking_date, start_time, end_time, purpose, status)
   VALUES (
     'Engineering Innovation Center',
     'Meeting Room A',
     'Gerard Way',
     '2026-11-20',
     '10:00',
     '12:00',
     'Placeholder Meeting',
     'confirmed'
   );
   ```

4. When prompted about Row Level Security (RLS), choose **Run without RLS** for local/classroom development (or configure public `SELECT` and `INSERT` policies if RLS is enabled).

---

### Step 3: Configure Environment Variables

1. In your Supabase project, go to **Project Settings** $\rightarrow$ **API Keys** (or **Data API**):
   - Copy your **Project URL** (e.g., `https://your-project-id.supabase.co` — do **not** include `/rest/v1/`).
   - Copy your **Publishable key** (`sb_publishable_...`) or legacy **`anon` `public`** key.
2. Create a `.env` file in the root of `room-booking-vue-app/` (next to `package.json`):

   ```env
   VITE_SUPABASE_URL=https://YOUR_PROJECT_ID.supabase.co
   VITE_SUPABASE_ANON_KEY=YOUR_PUBLISHABLE_OR_ANON_KEY
   ```

---

### Step 4: Configure Make.com Webhook Automation

To automatically log every new reservation into `booking_logs`:

1. **Create Make.com Trigger:**
   - Create a new scenario in Make.com and add **Webhooks $\rightarrow$ Custom webhook**.
   - Create and save the webhook, then copy its generated URL.
   - Click **Run once** so Make.com listens for a sample payload.
2. **Connect Supabase Database Webhook:**
   - In Supabase, go to **Integrations $\rightarrow$ Database Webhooks $\rightarrow$ Create webhook**.
   - Set **Table** to `bookings`, check **Insert**, set **Method** to `POST`, and paste your Make.com webhook URL.
3. **Map PostgreSQL Insert Action:**
   - Submit a test booking in your local Vue app so Make.com captures the `record` payload.
   - Add **PostgreSQL $\rightarrow$ Insert row into a table** after the Webhook module.
   - Connect using your Supabase **Transaction pooler** details (**Connect $\rightarrow$ Direct Connection string $\rightarrow$ Transaction pooler**, Port `6543`).
   - Select `public.booking_logs` and map:
     - `booking_id`: `{{1.record.id}}`
     - `event_type`: `BOOKING_CREATED`
     - `details`: `Room {{1.record.room_name}} at {{1.record.location}} reserved by {{1.record.booked_by}} on {{1.record.booking_date}}`
   - Save the scenario and switch it to **Active** (*Immediately as data arrives*).

---

### Step 5: Run the Application Locally

Start the Vite development server:

```bash
npm run dev
```

Open the local URL displayed in your terminal (typically `http://localhost:5173`) in your browser.

---

## Verification Checklist

- [ ] **Dashboard Load:** Existing records from `bookings` display on the main dashboard.
- [ ] **Create Reservation:** Clicking **+ Reserve** and submitting the form adds a new row to Supabase `bookings`.
- [ ] **Automation Trigger:** Submitting a booking triggers the Make.com scenario and inserts a linked row into `booking_logs` with a matching `booking_id`.
- [ ] **Mobile Responsiveness:** Resizing the browser below `768px` switches the booking table into stacked mobile cards and adapts the reservation modal.

---
GitHub Link: https://github.com/lolerwastaken/room-booking-vue-app
