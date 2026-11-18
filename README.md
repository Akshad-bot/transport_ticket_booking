# Ticket Booking - Multi-Modal Transport Ticketing & Real-Time Database Monitor

A full-stack, production-grade transport ticket booking web application with a built-in **Live Database Monitor**. Built with **FastAPI**, **SQLite**, and **Vanilla HTML5/CSS3/JavaScript** without frameworks or build tools.

All operations across booking and authentication (logins, failed security events, reservations, seat decrement/restoration) appear live in the admin monitor via 5-second polling.

---

## 🚀 Key Highlights & Enhancements

- **🇮🇳 India-Originated Live Transit Data**: Replaced all foreign mock data with 62+ authentic Indian domestic and international routes (Air India, IndiGo, Vistara, Akasa Air, IRCTC Vande Bharat Express, Rajdhani, KSRTC Volvo, MSRTC Shivneri, NueGo, Ola Prime, Uber Premier, BluSmart EV, Yulu Miracle EV).
- **✈️ Direct India to London Flight Corridors**: Full MakeMyTrip/Goibibo-style flight search from Delhi and Mumbai to London Heathrow (Air India AI 161, British Airways BA 142, Virgin Atlantic VS 301, Emirates via Dubai, IndiGo Codeshare via Istanbul).
- **₹ All Fares in Indian Rupees**: Every fare, summary breakdown, revenue card, and ticket is displayed in Indian Rupees (₹).
- **✨ Clean & Interactive Landing Page**: Added interactive city quick-chips (Delhi, Mumbai, Bengaluru, Hyderabad, London, Goa, etc.), trending route banners, and clear informative cards explaining the live SQLite database sync.
- **🏷️ Clean Brand Title**: Changed header and tab title to **"Ticket Booking"** plain and simple.
- **🚫 Removed "All Modes" Tab**: Kept direct, dedicated tabs for Flights, Trains, Buses, Cabs, and Bikes.

---

## 🗄️ Database Architecture

The SQLite database (`transport.db`) consists of four relational tables:

```
+-------------------------------------------------------------------------------------------------+
|                                         DATABASE SCHEMA                                         |
+-------------------------------------------------------------------------------------------------+
|                                                                                                 |
|   +-----------------------+              +--------------------------------------------------+   |
|   |         users         |              |                      trips                       |   |
|   +-----------------------+              +--------------------------------------------------+   |
|   | id (PK)               |<----+        | id (PK)                                          |   |
|   | name                  |     |        | transport_type (flight, bus, cab, bike, train)   |   |
|   | email (Unique)        |     |        | operator                                         |   |
|   | password_hash         |     |        | origin                                           |   |
|   | role (user / admin)   |     |        | destination                                      |   |
|   | created_at            |     |        | departure_time                                   |   |
|   +-----------------------+     |        | arrival_time                                     |   |
|               |                 |        | price (in INR / ₹)                               |   |
|               |                 |        | seats_total                                      |   |
|               | 1:N             |        | seats_available                                  |   |
|               v                 |        +--------------------------------------------------+   |
|   +-----------------------+     |                                 |                             |
|   |      login_logs       |     |                                 |                             |
|   +-----------------------+     |                                 | 1:N                         |
|   | id (PK)               |     |                                 |                             |
|   | user_id (FK, Nullable)|     |                                 v                             |
|   | email_tried           |     |        +--------------------------------------------------+   |
|   | login_time            |     |        |                     bookings                     |   |
|   | ip_address            |     |        +--------------------------------------------------+   |
|   | status (success/fail) |     |        | id (PK)                                          |   |
|   +-----------------------+     |        | user_id (FK -> users.id)                         |   |
|                                 +------->| trip_id (FK -> trips.id)                         |<--+
|                                          | passenger_names (Text)                           |
|                                          | seats_booked (Integer)                           |
|                                          | total_price (Float, in INR / ₹)                  |
|                                          | status (confirmed / cancelled)                   |
|                                          | booked_at (DateTime)                             |
|                                          +--------------------------------------------------+
+-------------------------------------------------------------------------------------------------+
```

---

## 💻 11 Frontend Pages Breakdown

### Customer Side (6 Pages)
1. **Home / Search (`/` or `/index.html`)**: Transport mode tabs (Flights, Trains, Buses, Cabs, Bikes), interactive city quick-chips, Departure date picker, Passengers selector, and popular Indian routes with starting fares in ₹.
2. **Search Results (`/search.html`)**: 2-column interface featuring filters on the left (transport type, departure time of day, max fare in ₹, sort order) and dynamic trip cards on the right with a "Book Ticket" button.
3. **Login / Register (`/login.html`)**: Centered dual-tab authentication card. Logs every authentication attempt (success and failed) with client IP to the database.
4. **Booking (`/booking.html`)**: Interactive seat selection grid for flights/trains/buses, quantity selector for cabs/bikes, dynamic passenger input fields, fare breakdown in ₹, and atomic confirmation.
5. **Ticket / Confirmation (`/ticket.html`)**: Printable travel boarding pass with booking reference (`#BK-XXXXX`), status badge, route details, and barcode mock.
6. **My Bookings (`/my-bookings.html`)**: List of user reservations with status badges and an instant "Cancel" button that atomically restores seats to the trip.

### Admin / Database Monitor Side (5 Pages with Shared Left Sidebar)
7. **Dashboard (`/admin/dashboard.html`)**: Four real-time aggregate cards (Users, Bookings, Revenue in ₹, Failed Logins Today), a breakdown table per transport type, and a unified recent activity stream.
8. **Users Table (`/admin/users.html`)**: Searchable, sortable, paginated directory of registered accounts. Never exposes password hashes.
9. **Login Logs Audit (`/admin/login-logs.html`)**: Live security monitor showing IP address, email tried, timestamp, and green "SUCCESS" / red "FAILED" status badges.
10. **Trips Inventory (`/admin/trips.html`)**: Real-time seat availability, pricing in ₹, occupancy %, and a "Sync from API" button that pulls destination weather telematics via Open-Meteo.
11. **Bookings Stream (`/admin/bookings.html`)**: Real-time table of all ticket bookings. **Newly added bookings are automatically highlighted with an amber fade-out animation.**

---

## 📦 Setup & Execution Guide

### 1. Start the Server (Already Running)
```powershell
.\venv\Scripts\uvicorn backend.main:app --host 127.0.0.1 --port 8000 --reload
```

### 2. Open in Browser
- **Customer Booking Site**: [http://127.0.0.1:8000/](http://127.0.0.1:8000/)
- **Admin Database Monitor**: [http://127.0.0.1:8000/admin/dashboard.html](http://127.0.0.1:8000/admin/dashboard.html)
- **Interactive Swagger API Docs**: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)

---

## 🔑 Seeded Login Credentials

| Role | Email | Password | Access Level |
|---|---|---|---|
| **Administrator** | `admin@transport.com` | `admin123` | Full Admin & Database Monitor access |
| **Customer** | `rahul@example.com` | `user123` | Booking, viewing tickets, cancellation |
| **Customer** | `priya@example.com` | `user123` | Booking, viewing tickets, cancellation |

---

## 🖥️ Live Two-Window Demo Script

1. **Window 1 (Admin Monitor)**: Open [http://127.0.0.1:8000/login.html](http://127.0.0.1:8000/login.html), log in as `admin@transport.com` / `admin123`, and go to **Bookings Stream** (`/admin/bookings.html`). Notice the live clock polling every 5 seconds.
2. **Window 2 (Customer Booking)**: Open an Incognito window, go to [http://127.0.0.1:8000/login.html](http://127.0.0.1:8000/login.html), and log in as `rahul@example.com` / `user123`.
3. **Flight to London Search**: In Window 2, click the **"View London Flights →"** banner or search from **Delhi** to **London**. You will see 5 suitable flight options (Air India Boeing 777, British Airways Dreamliner, Virgin Atlantic, etc.) with fares in ₹.
4. **Book a Ticket**: Select 2 seats on the visual seat map and book.
5. **Watch Window 1**: Within 5 seconds, the new booking appears with an **amber flash highlight** and the revenue counter in ₹ updates automatically!
