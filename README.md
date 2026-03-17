# Trippy Find — Travel Planning Platform

A full-stack travel platform that lets you search flights and hotels, compare real-time prices, and plan multi-destination trips — all in one place. Built in under 24 hours for SummerBuild 2025, winning the Gold Award 🥇.

💻 **Repo:** [github.com/darthrevan030/summerbuild2025](https://github.com/darthrevan030/summerbuild2025)  
🏆 **Devpost:** [devpost.com/software/404-not-found-insert-app-name-here](https://devpost.com/software/404-not-found-insert-app-name-here)  
🎥 **Demo:** [youtu.be/nVTvcB19b_4](https://youtu.be/nVTvcB19b_4)

---

## The Idea

A friend flew from India to Singapore via a ridiculous detour because it was the cheapest option. Most flight search engines optimise purely for price — but cheapest doesn't always mean best. We built Trippy to let users actually balance cost vs. journey time, not just sort by fare.

---

## Features

- **Flight search** across 400+ airlines with real-time pricing via Amadeus API
- **Hotel search** at the destination — flights and accommodation in one platform
- **Cost vs. time filter** — custom sorting logic to balance price and journey duration
- **Custom IATA lookup** — Amadeus free tier doesn't resolve airport names, so we built our own city → IATA code system backed by Supabase
- **Save favourites** for later comparison
- **Search history** — track previous searches for easy access
- **Caching layer** to reduce redundant API calls
- **Mobile responsive** across all devices

### Search Capabilities

- Filter by price, stops, airlines, and departure times
- Detailed flight info — duration, layovers, airline details
- Hotel ratings, amenities, and pricing
- Return date support and travel class selection
- Real-time availability for both flights and hotels

---

## Architecture

Trippy uses a 4-tier architecture:

```
┌─────────────────────────────────────────────┐
│              CLIENT (Browser)                │
│         React + Tailwind CSS                 │
│              Port 3000                       │
└─────────────────┬───────────────────────────┘
                  │ HTTP / REST
┌─────────────────▼───────────────────────────┐
│           API LAYER (Express)                │
│         Node.js + Express.js                 │
│              Port 8080                       │
│  /api/flights  /api/hotels  /api/users       │
└──────┬──────────────────────┬────────────────┘
       │                      │
┌──────▼──────┐     ┌─────────▼──────────────┐
│  AMADEUS    │     │       SUPABASE          │
│  API        │     │  User data, favourites  │
│  Flights +  │     │  search history,        │
│  Hotels     │     │  IATA code lookup       │
└─────────────┘     └────────────────────────┘
```

### Frontend (React)
- **Location:** `frontend/`
- **Entry:** `frontend/src/App.js`
- **Technologies:** React, Tailwind CSS, Lucide Icons
- **Port:** 3000

### Backend (Node.js/Express)
- **Location:** `backend/`
- **Entry:** `backend/server.js`
- **Technologies:** Express.js, Supabase, Amadeus API
- **Port:** 8080

---

## Project Structure

```
summerbuild2025/
├── frontend/
│   ├── src/
│   │   ├── App.js              # Main application component
│   │   ├── index.js            # React entry point
│   │   └── App.test.js
│   ├── public/
│   └── package.json
│
└── backend/
    ├── server.js               # Main server
    ├── server-minimal.js       # Minimal server for testing
    ├── test-server.js
    ├── controllers/
    │   ├── flightController.js
    │   ├── hotelController.js
    │   ├── routeController.js
    │   └── userController.js
    ├── routes/
    │   ├── flights.js
    │   ├── hotels.js
    │   ├── routes.js
    │   └── users.js
    ├── services/
    │   ├── amadeusService.js       # Amadeus API integration
    │   ├── supabaseService.js      # Supabase integration
    │   ├── cacheService.js         # Caching layer
    │   └── routeCalculatorService.js
    └── utils/
        └── envChecker.js
```

---

## Getting Started

### Prerequisites

- Node.js 14+
- Amadeus API credentials — [get them free](https://developers.amadeus.com)
- Supabase project — [supabase.com](https://supabase.com)

### Backend Setup

```bash
cd backend
npm install
cp .env.example .env
```

Configure `backend/.env`:

```env
PORT=8080
NODE_ENV=development
SUPABASE_URL=your_supabase_url
SUPABASE_ANON_KEY=your_supabase_anon_key
AMADEUS_CLIENT_ID=your_amadeus_client_id
AMADEUS_CLIENT_SECRET=your_amadeus_client_secret
CORS_ORIGIN=http://localhost:3000
```

```bash
npm run dev
```

### Frontend Setup

```bash
cd frontend
npm install
```

Configure `frontend/.env`:

```env
REACT_APP_API_URL=http://localhost:8080
```

```bash
npm start
```

Open [http://localhost:3000](http://localhost:3000).

---

## API Reference

### Flights

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/flights/search` | Search for flights |
| GET | `/api/flights/offers` | Get flight offers |
| GET | `/api/flights/airports` | Airport reference data |

### Hotels

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/hotels/search` | Search for hotels |
| GET | `/api/hotels/location` | Hotels near coordinates |
| GET | `/api/hotels/city/{city}` | Hotels in a specific city |

### Users

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/users/{id}/locations` | Get saved locations |
| POST | `/api/users/{id}/locations` | Save a location |
| GET | `/api/users/{id}/favorites` | Get favourites |
| POST | `/api/users/{id}/favorites` | Add to favourites |
| GET | `/api/users/{id}/profile` | Get user profile |
| PUT | `/api/users/{id}/profile` | Update user profile |

### System

| Method | Endpoint | Description |
|---|---|---|
| GET | `/health` | Health check |
| GET | `/api` | API documentation |
| GET | `/test` | Server test |
| GET | `/test-db` | Database connectivity check |

---

## Notable Implementation Details

### Custom IATA Code Lookup

The Amadeus free tier only accepts IATA codes — it doesn't resolve city names. We built a custom lookup system mapping natural language queries like "Singapore" or "London" to the correct IATA codes, stored in Supabase.

### Cost vs. Time Sorting

Custom sorting logic that scores flights on a weighted combination of price and total journey time. Users can adjust the weighting to prioritise whichever matters more.

### Caching Layer

Repeat queries are served from `cacheService.js` to reduce Amadeus API calls and stay within free tier rate limits.

---

## External Services

### Amadeus API
- **Purpose:** Real-time flight and hotel search
- **Service file:** `backend/services/amadeusService.js`
- **Setup:** [developers.amadeus.com](https://developers.amadeus.com)

### Supabase
- **Purpose:** User data, favourites, search history, IATA code lookup table
- **Service file:** `backend/services/supabaseService.js`
- **Setup:** [supabase.com](https://supabase.com)

---

## Testing

```bash
# Backend
cd backend && npm test

# Frontend
cd frontend && npm test

# Manual checks
curl http://localhost:8080/health
curl http://localhost:8080/test-db
```

---

## Challenges

- **Amadeus free tier limitations** — missing airport name resolution required building a custom lookup system from scratch
- **Routing configuration** — handling multiple concurrent API integrations with clean dynamic routing was the steepest learning curve
- **Competing with Google Flights** — forced us to focus on a genuinely differentiated feature (cost/time balance) rather than trying to match on breadth

---

## What's Next

- Direct booking via Amadeus enterprise API
- Seasonal pricing insights and fare drop alerts
- Real-time price trackers
- Visual maps showing flight paths and layover locations
- User reviews, lounge access, and baggage info

---

## Team

Built by [Samarth Bhatia](https://github.com/darthrevan030) and [Ming Yang Ang](https://github.com/AngMingYang) as Team 404 Not Found at SummerBuild 2025.

---

## License

MIT
