# GadgetGuru

GadgetGuru is a full-stack gadget recommendation and comparison platform built as a final year project and portfolio-ready application. It helps users discover phones and laptops, filter products by budget and preferences, compare devices side by side, and get ranked recommendations based on priorities such as gaming, camera, battery, and overall value.

The project has two main parts:

- A `FastAPI` backend that serves gadget data, search, comparison, and recommendation APIs
- A `React + Vite` frontend that provides the user interface for browsing, recommending, and comparing gadgets

## Project Highlights

- Unified gadget catalog for phones and laptops
- Ranked phone recommendations based on intent and constraints
- Device comparison flow for up to 3 gadgets
- MongoDB-backed product storage
- Fallback phone dataset support from `backend/phones_data.json`
- External API integration for phone specifications via RapidAPI
- Price refresh worker stub using Flask + SerpAPI logic
- Modern frontend with reusable components, hooks, and stateful compare/watchlist flows

## Repository Structure

```text
final year projects/
|- backend/
|  |- gadgetguru_api/
|  |- scripts/
|  |- tests/
|  |- requirements.txt
|  |- .env.example
|  `- phones_data.json
|- gadgetguru-frontend/
|  |- src/
|  |- package.json
|  `- vite.config.js
|- REFACTORED_FILES/
`- README.md
```

## Tech Stack

### Frontend

- React 19
- Vite
- Tailwind CSS
- Material UI
- Framer Motion
- React Router

### Backend

- FastAPI
- Python
- MongoDB with `pymongo`
- Pydantic
- Uvicorn
- Requests
- Flask (used for the separate price refresh worker)

## Core Features

### 1. Gadget Catalog

Users can browse available phones and laptops with pricing and specification data pulled from the backend.

### 2. Smart Recommendations

The backend ranks phone recommendations using preference-driven logic. Users can choose filters such as:

- budget
- brand
- minimum RAM
- minimum storage
- priority like `gaming`, `camera`, `battery`, or `balanced`

### 3. Device Comparison

Users can add up to 3 gadgets and compare them across multiple specification sections.

### 4. Search with API Fallback

If matching phones are not found in MongoDB, the backend can query an external RapidAPI phone specification source and persist normalized results.

### 5. Price Refresh Workflow

The repo also contains a separate Flask worker file for refreshing price information using SerpAPI logic. In the current FastAPI app, `/api/refresh-prices` accepts requests but does not run the external refresh worker itself.

## Local Setup

### Prerequisites

Install the following before running the project locally:

- Python 3.11+ recommended
- Node.js 18+ recommended
- npm
- MongoDB running locally or a MongoDB Atlas connection string

## Backend Setup

1. Open a terminal in [backend](backend).
2. Create and activate a virtual environment.
3. Install dependencies.
4. Create a `.env` file from `.env.example`.
5. Start the FastAPI server.

Example commands on Windows PowerShell:

```powershell
cd backend
py -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
uvicorn main:app --reload
```

The backend will run at:

```text
http://127.0.0.1:8000
```

Useful backend routes:

- `GET /`
- `GET /health`
- `GET /api/phones`
- `GET /api/laptops`
- `POST /api/recommendations`
- `POST /api/search`
- `POST /api/compare-devices`

## Backend Environment Variables

The backend uses the following environment variables:

```env
MONGO_URI=mongodb+srv://<username>:<password>@<cluster-url>/?appName=<app-name>
DB_NAME=gadgetguru
FRONTEND_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

Optional variables:

```env
RAPIDAPI_KEY=your_rapidapi_key
SERPAPI_KEY=your_serpapi_key
CREATE_INDEXES=true
```

Notes:

- `MONGO_URI` and `DB_NAME` are required for full database-backed functionality.
- If MongoDB is unavailable, the API can still start, but several endpoints will return `503`.
- `RAPIDAPI_KEY` is needed for external phone specs fallback and `/api/specs`.
- `SERPAPI_KEY` is used by the separate Flask price refresh worker logic.

## Frontend Setup

1. Open a second terminal in [gadgetguru-frontend](gadgetguru-frontend).
2. Install dependencies.
3. Start the Vite development server.

Example commands:

```powershell
cd gadgetguru-frontend
npm install
npm run dev
```

The frontend will usually run at:

```text
http://127.0.0.1:5173
```

## Frontend API Configuration

In development, the frontend defaults to:

```text
http://127.0.0.1:8000/api
```

This comes from [env.js](gadgetguru-frontend/src/config/env.js).

If needed, you can override it with a Vite environment variable such as:

```env
VITE_API_URL=http://127.0.0.1:8000/api
```

## Running the Full Project Locally

Start the backend first, then the frontend.

1. Run `uvicorn main:app --reload` inside `backend`
2. Run `npm run dev` inside `gadgetguru-frontend`
3. Open `http://127.0.0.1:5173`

## Testing

The repository includes backend tests for the recommendation ranking behavior.

Run from [backend](backend):

```powershell
python -m unittest tests.test_recommendation_engine
```

## Data and Import Notes

- Primary collection: `products`
- Backend fallback dataset: [phones_data.json](backend/phones_data.json)
- Import helper: [import_phones.py](backend/scripts/import_phones.py)

The import script upserts devices into MongoDB based on `model_name` or `brand + model`.

Example usage:

```powershell
cd backend
python scripts/import_phones.py --json .\phones_data.json
```

## API Overview

### Health and Status

- `GET /`
- `GET /health`

### Catalog

- `GET /api/phones`
- `GET /api/laptops`

### Search and Recommendations

- `GET /api/recommend?priority=gaming`
- `POST /api/recommend`
- `POST /api/recommendations`
- `POST /api/search`

### Comparison and Pricing

- `POST /api/compare-devices`
- `POST /api/refresh-prices`
- `GET /api/specs`

For the full backend endpoint reference, see [API_ENDPOINTS.md](backend/API_ENDPOINTS.md).

## Important Implementation Notes

- The main production-style backend app is [backend/gadgetguru_api/main.py](backend/gadgetguru_api/main.py).
- [backend/main.py](backend/main.py) exposes the FastAPI app entry point for Uvicorn.
- The ranking logic lives in `backend/gadgetguru_api/phone_ranking/`.
- The separate Flask price refresh worker is in [flask_refresh_prices.py](backend/gadgetguru_api/flask_refresh_prices.py).

## Academic and Portfolio Value

This project is suitable for:

- final year project demonstration
- resume and portfolio showcase
- full-stack development practice
- API integration and recommendation-system experimentation

It demonstrates work across frontend engineering, backend API design, database integration, ranking logic, and real-world product comparison workflows.

## Future Improvements

- add authentication and saved user profiles
- deploy backend and frontend for public access
- expand laptop recommendation ranking
- automate price refresh as a background service
- improve search with richer text queries and filters

## Author Notes

GadgetGuru is structured as a practical full-stack project with clear separation between UI, API, ranking logic, and data access. It is a good fit for both academic submission and GitHub presentation because it shows architecture, implementation depth, and usable product behavior in one repository.
