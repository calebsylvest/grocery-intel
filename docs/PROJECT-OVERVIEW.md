# Project Overview

## Project Purpose
A personal web application to reduce grocery spending through intelligent meal planning and multi-store price comparison. Initial target user: single household (Caleb) with potential for expansion.

## Core Problem Statement
Without systematic meal planning and price comparison, households:
- Overspend on groceries by 15-30%
- Make impulse purchases
- Waste time on meal decisions
- Don't optimize store selection based on actual prices

## Solution
A web-based tool that enables:
1. Recipe management with ingredient tracking
2. Weekly meal planning with visual calendar
3. Multi-store price comparison for shopping lists
4. Spending tracking and budget adherence
5. Historical analysis to identify savings opportunities

## Success Criteria (3 Month Test)
- Reduce monthly grocery spending by 15-20%
- Complete weekly meal planning in <30 minutes
- Track planned vs. actual spending with <5% margin of error
- Maintain consistent weekly usage (12 consecutive weeks)

## Non-Goals (MVP)
- Mobile native apps
- User authentication/multi-user support
- Automated price scraping
- Recipe import from URLs
- Nutritional tracking
- Pantry inventory automation
- Social features/recipe sharing

## Target Timeline
- **Weeks 1-2**: Database + Recipe/Food Item CRUD
- **Weeks 3-4**: Multi-store pricing + cost calculations
- **Weeks 5-6**: Meal planning calendar + shopping list generation
- **Weeks 7-8**: Spending tracking + basic analytics
- **Total**: 6-8 weeks to fully functional MVP

## Technology Stack
- **Backend**: Node.js + Express + TypeScript
- **Database**: PostgreSQL (local dev), Neon (production)
- **Frontend**: React + Vite + TypeScript + Tailwind CSS
- **Data Fetching**: TanStack Query (React Query)
- **Validation**: Zod (backend & frontend)
- **Code Quality**: ESLint + Prettier
- **Deployment**: Railway/Render (backend), Vercel (frontend), Neon (database)
- **Development**: Local-first, single user (no auth initially)
- **Future**: PWA support for offline shopping list access

## Key Metrics to Track
1. **Cost Savings**: Planned vs. Actual grocery spending per week
2. **Time Efficiency**: Minutes spent on meal planning per week
3. **Price Variance**: Cost differences between stores for same shopping list
4. **Recipe Utilization**: Frequency of recipe reuse
5. **Budget Adherence**: % of weeks staying under planned budget

## Repository Structure
```
grocery-intel/
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── services/
│   │   ├── schemas/       # Zod validation schemas
│   │   ├── middleware/
│   │   └── utils/
│   ├── migrations/
│   ├── tsconfig.json
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── types/         # TypeScript interfaces
│   │   └── utils/
│   ├── tsconfig.json
│   ├── vite.config.ts
│   └── package.json
├── docs/
│   ├── PROJECT-OVERVIEW.md
│   ├── REQUIREMENTS.md
│   ├── DATABASE-SCHEMA.md
│   ├── API-SPECIFICATION.md
│   ├── UI-SPECIFICATIONS.md
│   └── DEVELOPMENT-PLAN.md
├── .eslintrc.cjs
├── .prettierrc
└── README.md
```

## Development Principles
1. **Start Simple**: Manual data entry before automation
2. **Mobile-Responsive**: Web-first but mobile-friendly
3. **Real Data**: Use actual prices from local stores
4. **Iterative**: Build, test, refine in small cycles
5. **Pragmatic**: Accept imperfect unit conversions and data gaps