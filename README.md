#Try This :- https://wavetips-classicharsh.vercel.app/
# 🚀 WaveTips — Ultimate Creator Tipping & Real-Time Alert Platform

[![Next.js](https://img.shields.io/badge/Next.js-15-black?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org/)
[![Express.js](https://img.shields.io/badge/Express.js-5.x-lightgrey?style=for-the-badge&logo=express)](https://expressjs.com/)
[![Supabase](https://img.shields.io/badge/Supabase-PostgreSQL-green?style=for-the-badge&logo=supabase)](https://supabase.com/)
[![Razorpay](https://img.shields.io/badge/Razorpay-INR_Payments-blue?style=for-the-badge&logo=razorpay&logoColor=white)](https://razorpay.com/)
[![Socket.io](https://img.shields.io/badge/Socket.io-Realtime-black?style=for-the-badge&logo=socket.io)](https://socket.io/)
[![TailwindCSS](https://img.shields.io/badge/TailwindCSS-Aesthetics-38B2AC?style=for-the-badge&logo=tailwind-css)](https://tailwindcss.com/)

**WaveTips** is a high-performance, full-stack tipping and live-alert platform engineered specifically for Indian content creators and live streamers. It allows viewers to scan dynamic QR codes or visit customizable tipping links, pay instantly in INR via **UPI, Cards, Wallets, and Netbanking**, and trigger real-time alert overlays (sounds and themes) directly on OBS or Streamlabs.

I designed and built the entire ecosystem myself—from the multi-module backend and the interactive Next.js application to the automated payment commission ledger and database schemas.

---

## 📌 Project Architecture

The repository contains a cohesive, multi-component architecture, structured into specific directories:

```
streamtips/
├── client/                 # Next.js frontend (App Router, Tailwind, Framer Motion)
├── backend/                # Production-grade Node.js + TypeScript + Express + Supabase API
├── payment/                # Standalone Vanilla HTML/CSS/JS + Express + Razorpay checkout prototype
├── server/                 # Standalone Express + MongoDB + Socket.io realtime server
├── supabase_full_schema.sql # Database schema with RLS policies, tables, and triggers
└── README.md               # Main project documentation (this file)
```

---

## ✨ Core Features & Workflows

### 1. The Automated Commission Model
The platform operates on a configurable revenue split (e.g., **10% platform fee, 90% creator earnings**). 
* When a viewer tips, the payment goes directly to the main platform's Razorpay merchant account.
* The system performs server-side calculations to log the commission breakdown:
  $$\text{Total Amount (e.g., ₹100)} \rightarrow \text{Platform Fee (₹10)} + \text{Creator Earnings (₹90)}$$
* A ledger tracking earnings and fees is recorded in the database, allowing simplified creator payouts.

### 2. QR Code Generator & Dynamic QR Tips
* Creators can generate custom, high-speed QR codes matching specified presets or custom parameters.
* Scanning the QR code resolves instantly to their tipping page, auto-filling payment information for the viewer.

### 3. Real-Time OBS Alert Overlay Widgets
* Supports browser-source alerts for streaming software like OBS Studio, vMix, and Streamlabs.
* Once a tip is verified via HMAC signature, a real-time event triggers audio-visual notification overlays.
* Interactive custom styles include `default`, `neon`, `minimal`, `fireworks`, and `gaming` themes.

### 4. Interactive Live Leaderboard
* Features public, animated leaderboards showing the **Top Streamers** (creators receiving the most tips) and the **Top Supporter Viewers**, driving engagement through gamification.

---

## 🛠 Tech Stack Breakdown

### Frontend (Client)
* **Framework:** Next.js (App Router, strict TypeScript)
* **Styling:** Custom Tailwind CSS, CSS Glassmorphism
* **Animations:** Framer Motion (smooth hover states, layout transitions, dynamic alerts)
* **Icons:** Lucide React
* **Authentication:** Supabase Auth Integration

### Primary API (Backend)
* **Environment:** Node.js + Express.js in TypeScript
* **Database:** Supabase (PostgreSQL)
* **Validation:** Zod (for request validation and type safety)
* **Security:** Helmet (HTTP security headers), CORS whitelist, Express Rate Limiter
* **Payments:** Razorpay Node SDK (Order creation, HMAC-SHA256 signature verification)

### Standalone Prototypes & Legacy Services
* **`payment` Gateway Prototype:** A lightweight Express-based gateway utilizing vanilla HTML, CSS, and JS. Stores sessions and ledger payments in-memory, serving as an easily deployable Razorpay checkout module.
* **`server` (Realtime Server):** Node.js runtime with Express, MongoDB (Mongoose schemas), and Socket.io for immediate WebSocket connections to broadcast alerts to streamer overlay clients.

---

## 📊 Database Schema (Supabase PostgreSQL)

The backend utilizes a secure PostgreSQL instance managed via Supabase, with **Row Level Security (RLS)** and automated triggers:

* **`profiles`:** Stores creator profiles mapping 1:1 to Supabase `auth.users`.
* **`creator_settings`:** Stores UPI IDs, minimum tip values, custom alert themes (`neon`, `fireworks`, etc.), custom sound clips, and page messages.
* **`tips`:** Detailed payment records containing creator IDs, sender names, messages, amounts, platform fees, creator earnings, and verified Razorpay transaction identifiers.
* **`payment_codes`:** Holds information about active QR codes and link parameters.
* **`payment_notifications`:** Real-time log of unread tipping alerts.

> 📝 **Automation Trigger:** A database trigger `handle_new_user_settings()` automatically runs on `profiles` inserts, provisioning a default `creator_settings` row for newly registered creators.

---

## ⚡ Quick Start & Deployment Guide

### Configuration Setup
Make sure to create `.env` files based on the `.env.example` templates in the respective folders:

```env
# Backend API (.env)
PORT=5000
NODE_ENV=development
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
RAZORPAY_KEY_ID=your_razorpay_key_id
RAZORPAY_KEY_SECRET=your_razorpay_key_secret
PLATFORM_COMMISSION_PERCENT=10
CORS_ORIGIN=http://localhost:3000

# Next.js Client (.env.local)
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
NEXT_PUBLIC_SERVER_URL=http://localhost:5000
```

### 1. Database Setup
1. Create a new project on [Supabase](https://supabase.com).
2. Navigate to the SQL Editor in your Supabase dashboard.
3. Paste the contents of [supabase_full_schema.sql](file:///e:/streamtips/streamtips/supabase_full_schema.sql) and execute the queries to instantiate the database structure and trigger handlers.

### 2. Running the API Backend
```bash
cd backend
npm install
npm run dev
# Starts at http://localhost:5000
```

### 3. Running the Next.js Client
```bash
cd client
npm install
npm run dev
# Starts at http://localhost:3000
```

### 4. Running the Standalone Payment Gateway (Razorpay Playground)
```bash
cd payment
npm install
npm start
# Starts a lightweight portal at http://localhost:3000 with interactive streamer dashboards
```

### 5. Running the Realtime Socket.io Server
```bash
cd server
npm install
npm run dev
# Starts WebSocket broadcaster at http://localhost:5000
```

---

## 🔒 Security Implementations

* **HMAC Verification:** The application generates a cryptographic SHA-256 signature server-side combining the Razorpay order ID and payment ID, cross-referencing it against the signature returned by Razorpay Checkout before recording the tip.
* **Row Level Security (RLS):** All Supabase tables are guarded with RLS policies, ensuring public users can only read authorized details, while creators can only query and mutate their own profile settings.
* **CORS & Rate Limiting:** Restricts API endpoint exposure to verified web origins and limits spamming attempts with an IP-based request threshold.

---

## 🙋‍♂️ Built by Me

I designed and coded this entire platform to provide a beautiful, seamless, and fee-transparent tipping solution for streamers. 

* **GitHub:** [@yourusername](https://github.com/yourusername)
* **LinkedIn:** [Your Name](https://linkedin.com/in/yourname)
* **Portfolio:** [yourportfolio.dev](https://yourportfolio.dev)
* **Email:** your.email@example.com

Feel free to reach out for suggestions, collaborations, or custom feature requests!
