# MealWise

**An AI-powered local food discovery and delivery comparison agent built at the CMU Silicon Valley × NewsBreak AI for Local Life Hackathon.**

MealWise helps budget-conscious users answer a surprisingly difficult question: **“What can I actually afford to eat once delivery fees, service fees, tax, and tip are included?”**

Instead of comparing menu prices alone, MealWise interprets natural-language food requests, understands constraints such as budget and dietary preferences, finds matching meals, and compares the **total cost of the same meal across multiple delivery options**.

Our team built MealWise during the **CMU Silicon Valley ECE Hackathon with NewsBreak**, where the challenge was to create an AI agent that solves a real problem in everyday local life.

**🏆 Top 10 Project** — our team was the only Top 10 team composed entirely of incoming Fall 2026 students, and this was our first hackathon.

## 🔗 Live Demo

[**Try MealWise →**](https://newsbreak-hackathon-0919.onrender.com/)

> The current public demo uses simulated delivery quotes and clearly labels them as such. It does not claim live delivery availability or checkout pricing.

---

## The idea

Food delivery pricing is fragmented. A meal that looks inexpensive on the menu can become much more expensive after delivery fees, service fees, taxes, and tips. Comparing platforms manually also means repeatedly searching for the same restaurant and reconstructing the final price.

MealWise turns that process into one workflow:

```text
Describe what you want
        ↓
Understand intent and constraints
        ↓
Find matching meals
        ↓
Compare delivery options
        ↓
Rank by fit and total cost
        ↓
Explain the best-value choices
```

A user can start with something simple like:

> “I want a cheap vegan dinner under $20.”

MealWise converts that request into structured constraints and uses them throughout the recommendation pipeline.

---

## What we built

### 🔎 Natural-language food search

Users can search by craving or occasion rather than knowing exactly what restaurant or dish they want.

MealWise interprets signals such as:

- Budget
- Dietary preference
- Cuisine or food preference
- Restaurant scope
- Servings
- Delivery provider
- ETA constraints

Users can also refine a request through the conversational interface without having to restate every preference.

### 💰 Total-cost comparison

MealWise compares the cost users actually care about:

```text
Total =
  Items
+ Delivery
+ Service Fee
+ Tax
+ Tip
- Discount
```

Rather than declaring a restaurant “cheap” based only on its menu price, the system compares complete simulated totals under consistent assumptions.

### 🍽️ Best match at each restaurant

Instead of returning a long list of unrelated offers, MealWise finds the best matching meal at each restaurant and presents the results as a shortlist.

For each restaurant, users can see:
- The matched meal
- Estimated delivery time
- Total price
- Delivery provider
- Alternative delivery options
- Full fee breakdown
- Source information when available

The lowest-cost option is highlighted while alternatives remain visible for comparison.

### 🤖 AI where it helps — deterministic logic where it matters

MealWise uses an AI agent to understand natural-language requests, but it does **not** ask the model to invent prices or decide arithmetic.

The system separates:

**AI reasoning**
- Intent extraction
- Natural-language understanding
- Follow-up interpretation

from:

**Deterministic application logic**
- Dietary filtering
- Budget filtering
- Restaurant matching
- Quote calculations
- Offer validation
- Ranking

This gives us the flexibility of conversational AI without making pricing behavior opaque or unpredictable.

### 🥬 Inclusive dietary discovery

Dietary preferences are part of the recommendation pipeline rather than an afterthought in the UI.

The experience was designed to make constraints such as vegetarian and vegan preferences easy to express and visible throughout the search flow.

### 💬 Stateful conversational refinement

MealWise also supports multi-turn requests.

For example:

```text
User: Find me a cheap vegan dinner.

User: Actually, keep it under $15.

User: And I'd prefer something with rice.
```

The system preserves relevant constraints across turns while allowing new messages to override previous preferences.

---

## Product experience

The main interaction is intentionally simple:

```text
01 / Your order

What are we finding?

Craving or occasion
[ All restaurants ]

Demo total budget
[ $35 ]

Dietary
[ No preference ]

[ Find my best value → ]
```

Results are organized around:

```text
02 / Your shortlist

Best match at each restaurant
```

Each restaurant card compares multiple delivery options for the same meal, making it easy to see how fees affect the final price.

Users can then open a detailed view to inspect the restaurant, meal, ETA, source, provider comparison, and complete price breakdown.

---

## Architecture

MealWise is built as a full-stack TypeScript application with a shared contract between the client and server.

```text
                         ┌─────────────────────┐
                         │    React + Vite     │
                         │      Frontend       │
                         └──────────┬──────────┘
                                    │
                         /api/plan  │  /api/chat
                                    │
                         ┌──────────▼──────────┐
                         │  Express API Layer  │
                         └──────────┬──────────┘
                                    │
                    ┌───────────────┴───────────────┐
                    │                               │
           ┌────────▼────────┐             ┌────────▼────────┐
           │    AI Agent     │             │ Structured Data │
           │                 │             │   + Providers   │
           │ Intent parsing  │             │                 │
           │ Conversation    │             │ Offers / Quotes │
           └────────┬────────┘             └────────┬────────┘
                    │                               │
                    └───────────────┬───────────────┘
                                    │
                           ┌────────▼────────┐
                           │ Search, Filter  │
                           │    & Ranking    │
                           └────────┬────────┘
                                    │
                           ┌────────▼────────┐
                           │    MongoDB      │
                           │  Persistence    │
                           └─────────────────┘
```

The repository keeps the major concerns separated:

```text
.
├── client/
│   └── src/
│       ├── api/              # Planner and conversational API clients
│       ├── components/       # Search, chat, and comparison UI
│       ├── data/             # Frontend food-photo mappings
│       └── types/            # Client-facing TypeScript types
│
├── server/
│   ├── agent/                # Intent, search, model, and simulation logic
│   ├── data/                 # Demo offers, quotes, and menu data
│   ├── db/                   # MongoDB persistence
│   ├── middleware/           # Security middleware
│   ├── providers/            # Normalized provider abstraction
│   ├── routes/               # Planner and chat endpoints
│   ├── scripts/              # Data and development utilities
│   └── test/                 # Backend and ranking tests
│
└── shared/
    ├── intent.ts
    ├── quotes.ts
    └── schemas.ts            # Shared client/server contracts
```

---

## Tech stack

| Area | Technology |
| --- | --- |
| Frontend | React, Vite, TypeScript |
| Backend | Node.js, Express, TypeScript |
| AI | Gemini / OpenAI with local fallback |
| Validation | Zod |
| Database | MongoDB |
| API Design | Shared TypeScript/Zod contracts |
| Testing | Node test runner, Supertest |
| UI | Responsive custom CSS, Lucide React |
| CI | GitHub Actions |
| Deployment | Render |

---

## Engineering decisions

### Structured AI output instead of free-form recommendations

The model interprets the user's request into structured intent. Search, filtering, pricing, and ranking then operate on validated application data.

This prevents the recommendation layer from treating model-generated prices or availability as facts.

### Shared contracts across the stack

Core intent, quote, and request/response schemas live in `shared/`, reducing drift between the React client and Express server.

### Provider abstraction

Delivery-provider data is normalized behind a provider interface. The current demo uses mock/simulated provider data, but the architecture keeps provider-specific behavior separate from ranking and UI logic.

### Explainable ranking

Recommendation behavior is deterministic and testable. Budget and dietary constraints are applied explicitly, and the UI exposes the price components behind the final ranking.

### Honest demo data

We deliberately distinguish among sourced menu references, simulated delivery quotes, and hypothetical restaurant delivery.

The interface uses labels such as **Demo**, **Simulated**, and **Illustrative photo** rather than presenting hackathon data as live commercial data.

---

## Data and demo scope

The current comparison demo includes **15 restaurants × 3 delivery options**.

Four restaurant scenarios use collected public menu references, while eleven are explicitly fictional demo scenarios. Delivery totals are simulated for the hackathon and do not establish real-time provider or restaurant availability.

This allowed us to prototype and evaluate the complete recommendation experience without pretending to have live commercial integrations.

---

## Testing

The backend test suite covers the parts of the system where correctness matters most:

- Agent behavior
- API behavior
- Menu processing
- Quote validation
- Restaurant ranking
- Pricing simulation
- Three-platform comparison

Run the test suite with:

```bash
npm test --workspace server
```

Build the complete project with:

```bash
npm run build
```

We also use GitHub Actions to validate pushes and pull requests.

---

## Run locally

### Requirements

- Node.js 22+
- npm

Install dependencies and start the development environment:

```bash
npm install
npm run dev
```

The frontend runs at:

```text
http://localhost:5173
```

and the API runs at:

```text
http://localhost:8787
```

For MongoDB-backed persistence, copy `.env.example` to `.env` and configure the required environment variables.

Without MongoDB, the development environment can use the local demo data for a disposable session.

---

## API overview

MealWise exposes two main application workflows.

### `POST /api/plan`

Creates a structured recommendation plan from a search request.

Example:

```json
{
  "prompt": "Dinner for two tonight",
  "budget": 25,
  "dietary": "Vegetarian"
}
```

The response contains ranked restaurant options, interpreted constraints, pricing information, data provenance, and a plain-language summary.

### `POST /api/chat`

Supports conversational refinement of the same recommendation flow.

Example:

```json
{
  "message": "Find me a cheap vegan dinner",
  "budget": 15,
  "dietary": "Vegan"
}
```

A `conversationId` allows later messages to preserve relevant preferences while overriding constraints the user explicitly changes.

---

## Hackathon experience

MealWise was built for the **AI for Local Life — CMU Silicon Valley ECE Hackathon with NewsBreak**, where teams were challenged to build an AI agent that solves a meaningful problem in everyday local life.

Our five-person team took the project from an initial idea to a working, deployed full-stack prototype during the hackathon.

Beyond implementing the application, we:

- Researched and organized real public menu references to ground parts of the demo.
- Compared and normalized data from different restaurant and delivery scenarios.
- Designed a transparent multi-provider pricing model.
- Built the AI intent and conversational workflow.
- Connected the React frontend to the agent, ranking, API, and persistence layers.
- Built and populated the backend database/data pipeline.
- Iterated on the product after considering users with dietary constraints, including vegetarian and vegan preferences.
- Added explicit provenance and simulation disclosures instead of presenting synthetic data as live information.
- Deployed a working end-to-end demo.

The project was selected as a **Top 10 project**. There was no ranking within the Top 10.

Our team was also the **only Top 10 team made up entirely of incoming Fall 2026 students**, and it was our first hackathon.

For us, the most valuable part of the experience was not simply getting an AI model to respond. It was learning how to turn an open-ended idea into a coherent product across **UX, AI, data, backend engineering, validation, and deployment** under a very short deadline.

---

## What I worked on

My contributions focused on turning the team's ideas and individual components into a working end-to-end product.

I worked across the stack to:

- **Integrated the frontend and backend**, connecting the user-facing search and comparison experience to the recommendation pipeline.
- **Contributed to the AI integration**, helping refine how user requests and preferences flow into the agent and structured recommendation logic.
- **Co-built the backend data/database layer**, helping organize the data used by the recommendation and comparison system.
- **Collected, compared, and archived public restaurant/menu references** to ground the demo in real examples rather than relying entirely on generated data.
- **Contributed product ideas and frontend improvements**, including better support for users with dietary preferences such as vegetarian and vegan choices.
- **Worked closely with the team across frontend, backend, AI, and data boundaries** to get the complete prototype integrated and ready for the final demo.

The hackathon was especially valuable because it pushed me beyond implementing an isolated feature. I had to understand how the frontend, APIs, agent, ranking logic, data model, and product experience affected one another — and help turn those pieces into something people could actually use.

---

## What we'd build next

The biggest next step is replacing simulated delivery quotes with authorized real-time provider integrations.

That would make it possible to incorporate:

- Live delivery availability
- Location-specific fees
- Current promotions
- More accurate ETAs
- Account-specific eligibility
- Restaurant availability

We would also like to expand personalization so MealWise can learn from previous choices while still keeping the reasoning behind recommendations transparent.

---
