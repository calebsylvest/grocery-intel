# Development Plan

## Overview
8-week development plan for Grocery Intelligence MVP. Assumes ~20 hours/week development time (160 hours total).

## Week-by-Week Breakdown

---

### Week 1: Project Setup + Database + Backend Foundation

**Goals**:
- Set up development environment
- Initialize database
- Build basic API scaffolding
- Implement first CRUD endpoints

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] Create GitHub repository
- [ ] Initialize backend (`npm init`, install dependencies)
  - Express, pg (PostgreSQL client), dotenv, cors, nodemon
- [ ] Initialize frontend (`create-react-app` or Vite)
  - Tailwind CSS setup
  - React Router
  - Axios for API calls
- [ ] Set up PostgreSQL database (local or Neon)
- [ ] Run database initialization script (from DATABASE-SCHEMA.md)
- [ ] Seed database with sample data
- [ ] Configure environment variables (.env)

**Wednesday-Thursday** (8 hours):
- [ ] Backend folder structure
```
  /backend
    /src
      /routes
      /controllers
      /models
      /services
      /utils
      app.js
      server.js
```
- [ ] Implement Food Items endpoints
  - GET /api/food-items
  - GET /api/food-items/:id
  - POST /api/food-items
  - PUT /api/food-items/:id
  - DELETE /api/food-items/:id
- [ ] Test with Postman/Thunder Client

**Friday-Weekend** (4 hours):
- [ ] Implement Stores endpoints
  - GET /api/stores
  - POST /api/stores
  - PUT /api/stores/:id
  - DELETE /api/stores/:id (soft delete)
- [ ] Write basic error handling middleware
- [ ] Set up CORS for frontend

**Deliverables**:
- Working backend with Food Items + Stores CRUD
- Database initialized with seed data
- Postman collection for testing

---

### Week 2: Recipes Backend + Frontend Foundation

**Goals**:
- Complete Recipe API endpoints (with ingredients)
- Set up frontend routing and layout
- Build first UI screens

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] Implement Recipes endpoints
  - GET /api/recipes (with optional include_ingredients)
  - GET /api/recipes/:id (with ingredients)
  - POST /api/recipes (with ingredients in transaction)
  - PUT /api/recipes/:id (replace ingredients)
  - DELETE /api/recipes/:id
- [ ] Test recipe creation with multiple ingredients
- [ ] Handle edge cases (no ingredients, duplicate ingredients)

**Wednesday-Thursday** (8 hours):
- [ ] Frontend layout structure
  - App shell with sidebar/bottom nav
  - Routing setup (/, /recipes, /meal-plans, /stores, /analytics)
  - Mobile-responsive navigation
- [ ] Create shared components
  - Button (primary, secondary, danger)
  - Card
  - Input, Select, TextArea
  - Modal wrapper
  - Loading spinner
- [ ] Build Recipes List page
  - Fetch from API
  - Display recipe cards
  - Search functionality (client-side for MVP)
  - Navigate to detail on click

**Friday-Weekend** (4 hours):
- [ ] Build Recipe Detail page
  - Fetch single recipe with ingredients
  - Display ingredients list
  - Display instructions
  - Edit/Delete buttons (navigate to form)
- [ ] Basic styling with Tailwind
- [ ] Loading and empty states

**Deliverables**:
- Recipes API fully functional
- Frontend shell with navigation
- Recipe list + detail pages working

---

### Week 3: Recipe Form + Price Management

**Goals**:
- Build recipe create/edit form
- Implement price tracking endpoints
- Start price entry UI

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] Recipe Create/Edit Form
  - Text inputs for name, description, servings, times
  - Select for cuisine type
  - Dynamic ingredient rows
    - Food item autocomplete/select
    - Quantity + unit inputs
    - Add/remove ingredient buttons
  - Instructions textarea
  - Form validation
  - Save to API (POST or PUT)
- [ ] Handle form state (useState or React Hook Form)
- [ ] Success/error feedback (toast notifications)

**Wednesday-Thursday** (8 hours):
- [ ] Implement Prices endpoints
  - GET /api/prices/latest (with filters)
  - GET /api/prices/history/:food_item_id
  - POST /api/prices
  - PUT /api/prices/:id
  - DELETE /api/prices/:id
- [ ] Test price history queries
- [ ] Ensure latest price logic works correctly

**Friday-Weekend** (4 hours):
- [ ] Build Stores Management page
  - List all stores
  - Add store form (modal)
  - Edit store
  - Soft delete (deactivate)
- [ ] Build Price Entry UI (basic)
  - List food items
  - For each item, show current price per store
  - Quick edit inline or modal
  - Bulk price entry form (select store, enter prices for multiple items)

**Deliverables**:
- Recipe form fully functional
- Prices API working
- Basic price entry UI

---

### Week 4: Meal Planning Backend + UI

**Goals**:
- Implement meal plan + meal plan items endpoints
- Build meal plan calendar view
- Enable adding recipes to meal plans

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] Implement Meal Plans endpoints
  - GET /api/meal-plans (with filters)
  - GET /api/meal-plans/:id (with meals and cost calculation)
  - POST /api/meal-plans
  - PUT /api/meal-plans/:id
  - DELETE /api/meal-plans/:id
- [ ] Implement cost calculation service
  - Get latest prices for recipe ingredients
  - Calculate recipe cost
  - Sum across meal plan
- [ ] Implement Meal Plan Items endpoints
  - POST /api/meal-plans/:id/meals
  - PUT /api/meal-plan-items/:id
  - DELETE /api/meal-plan-items/:id

**Wednesday-Thursday** (8 hours):
- [ ] Build Meal Plan Calendar (Desktop)
  - 7-column grid for week
  - Display meals as cards in each day
  - Show date, recipe name, cost
  - "Add Meal" button on empty days
- [ ] Build Meal Plan Calendar (Mobile)
  - Vertical list by day
  - Meal cards stack within day
  - Add meal button per day
- [ ] Add Meal Modal
  - Select recipe from list
  - Choose meal type (dropdown)
  - Set servings (number input)
  - Save to backend

**Friday-Weekend** (4 hours):
- [ ] Meal Plan creation flow
  - Create new meal plan (name, date range)
  - Immediately navigate to calendar view
  - Add meals interactively
- [ ] Edit meal (change servings, notes)
- [ ] Remove meal from plan
- [ ] Display total cost in header

**Deliverables**:
- Meal planning API complete
- Calendar UI functional (desktop + mobile)
- Can create week plan and add recipes

---

### Week 5: Shopping List + Multi-Store Comparison

**Goals**:
- Generate shopping list from meal plan
- Calculate costs across multiple stores
- Display store comparison

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] Implement Shopping List endpoint
  - GET /api/meal-plans/:id/shopping-list
  - Aggregate ingredients from all recipes in plan
  - Group by food item (sum quantities)
  - Fetch latest prices from all stores
  - Calculate total cost per store
  - Return structured data (by category, with prices)
- [ ] Test with meal plan containing multiple recipes
- [ ] Verify quantity aggregation logic

**Wednesday-Thursday** (8 hours):
- [ ] Build Shopping List UI
  - Store cost comparison cards at top
  - Highlight cheapest store
  - Store selector dropdown (changes prices displayed)
  - Grouped by category (collapsible sections)
  - Shopping list items with checkboxes
  - Show quantity, unit price, total per item
  - "Used in" recipes listed per item
- [ ] Implement checkbox state (local storage for session persistence)
- [ ] Print stylesheet for list

**Friday-Weekend** (4 hours):
- [ ] Refine shopping list UX
  - Loading state while calculating
  - Empty state if no meal plan meals
  - Mobile optimization (horizontal scroll for store cards)
- [ ] Add "Mark Trip Complete" button
  - Opens shopping trip entry modal

**Deliverables**:
- Shopping list generation working
- Multi-store cost comparison visible
- Clear indication of best store

---

### Week 6: Shopping Trip Tracking + Dashboard

**Goals**:
- Implement shopping trip recording
- Build dashboard with summary stats
- Display recent trips and savings

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] Implement Shopping Trips endpoints
  - GET /api/shopping-trips (with filters)
  - POST /api/shopping-trips
  - PUT /api/shopping-trips/:id
  - DELETE /api/shopping-trips/:id
- [ ] Calculate variance (planned vs actual)
- [ ] Test trip recording flow

**Wednesday-Thursday** (8 hours):
- [ ] Shopping Trip Entry Form
  - Modal or dedicated page
  - Select store, date
  - Display planned cost (read-only)
  - Input actual cost
  - Auto-calculate variance (with color coding)
  - Notes textarea
  - Save to backend
- [ ] Success feedback + navigation
- [ ] Display trip in recent trips list

**Friday-Weekend** (4 hours):
- [ ] Build Dashboard page
  - Weekly summary card (current meal plan)
  - Spending summary card (planned vs actual)
  - Recent shopping trips table (last 5)
  - Quick stats panel (avg weekly spend, most used store, etc.)
- [ ] Fetch data from backend
- [ ] Make cards clickable (navigate to details)

**Deliverables**:
- Shopping trip tracking functional
- Dashboard showing overview stats
- User can see savings at a glance

---

### Week 7: Analytics + Polish

**Goals**:
- Implement analytics endpoints
- Build analytics dashboard
- Add loading/error/empty states everywhere
- Fix bugs

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] Implement Analytics endpoints
  - GET /api/analytics/spending-summary (with date filters)
  - GET /api/analytics/recipe-costs
- [ ] Test with historical data (create multiple past trips)
- [ ] Verify calculations (totals, averages, percentages)

**Wednesday-Thursday** (8 hours):
- [ ] Build Analytics Dashboard
  - Time period selector (last 7/30/90 days)
  - Spending over time chart (Recharts line/bar chart)
  - Top expensive recipes table
  - Most budget-friendly recipes table
  - Store comparison stats
- [ ] Mobile responsive charts
- [ ] Loading states for chart data

**Friday-Weekend** (4 hours):
- [ ] Add loading states to all pages
  - Skeleton loading for lists
  - Spinners for actions (save, delete)
- [ ] Add empty states
  - No recipes yet
  - No meal plans
  - No shopping trips
  - Include "Get Started" CTAs
- [ ] Error handling
  - API error responses
  - Toast notifications for errors
  - Form validation feedback

**Deliverables**:
- Analytics page with charts and stats
- All pages have proper loading/empty/error states
- UI feels polished and complete

---

### Week 8: Testing + Deployment + Documentation

**Goals**:
- End-to-end testing of complete workflow
- Fix remaining bugs
- Deploy to production
- Write user documentation

**Tasks**:

**Monday-Tuesday** (8 hours):
- [ ] End-to-end workflow test
  1. Create 5-10 recipes
  2. Enter prices for 2-3 stores
  3. Create meal plan for week
  4. Add recipes to each day
  5. Generate shopping list
  6. Compare store costs
  7. Record shopping trip
  8. View dashboard
  9. Check analytics
- [ ] Document bugs found
- [ ] Prioritize and fix critical bugs

**Wednesday-Thursday** (8 hours):
- [ ] Deployment preparation
  - Environment variables documented
  - Database migration scripts ready
  - Build scripts tested
- [ ] Deploy backend
  - Railway or Render
  - Connect to Neon PostgreSQL
  - Test API endpoints in production
- [ ] Deploy frontend
  - Vercel or Netlify
  - Configure API base URL
  - Test routing and functionality

**Friday-Weekend** (4 hours):
- [ ] Write README.md
  - Project description
  - Setup instructions (local development)
  - Deployment notes
  - Technology stack
- [ ] Write USER-GUIDE.md
  - How to add recipes
  - How to plan meals
  - How to use shopping lists
  - How to track spending
- [ ] Final testing in production
- [ ] Celebrate! 🎉

**Deliverables**:
- Deployed MVP accessible via URL
- Complete workflow tested
- Documentation written
- Ready for personal use

---

## Post-MVP Enhancements (Future Weeks)

### Phase 2 (Weeks 9-12):
- Recipe import from URL (web scraping)
- Barcode scanner for price entry (mobile PWA)
- Receipt OCR for automatic trip entry
- Pantry inventory tracking
- Better unit conversion logic
- Export shopping list to PDF

### Phase 3 (Weeks 13-16):
- User authentication (if sharing with spouse/family)
- Budget reverse planning ("I have $75, suggest meals")
- Automated price scraping (store APIs or web scraping)
- Price history trends and forecasting
- Deal alerts (price drops)
- Recipe recommendations based on budget

---

## Development Tools

### Backend
- **Node.js**: v18+ (LTS)
- **Express**: Web framework
- **pg**: PostgreSQL client
- **dotenv**: Environment variables
- **cors**: CORS middleware
- **nodemon**: Dev server auto-restart

### Frontend
- **React**: v18+
- **React Router**: v6
- **Tailwind CSS**: v3
- **Axios**: HTTP client
- **Recharts**: Chart library (analytics)
- **Heroicons**: Icon library
- **date-fns**: Date manipulation

### Database
- **PostgreSQL**: v14+
- **Neon** (recommended) or local PostgreSQL

### Development
- **VSCode**: Editor
- **Postman/Thunder Client**: API testing
- **Git**: Version control
- **GitHub**: Repository hosting

### Deployment
- **Backend**: Railway or Render (free tier)
- **Frontend**: Vercel or Netlify (free tier)
- **Database**: Neon (free tier, 512MB)

---

## Testing Strategy

### Manual Testing (MVP)
- Click-through all features after each build
- Test on mobile (Chrome DevTools responsive mode)
- Test edge cases:
  - Empty states
  - Large datasets (100+ recipes)
  - Invalid inputs
  - Network errors

### Automated Testing (Future)
- Unit tests for cost calculation logic
- Integration tests for API endpoints
- E2E tests with Playwright or Cypress

---

## Git Workflow

### Branching Strategy
```
main (production)
  ↓
develop (integration)
  ↓
feature/* (individual features)
```

### Commit Messages
```
feat: add recipe creation form
fix: correct price calculation bug
refactor: simplify shopping list aggregation
docs: update API specification
style: improve mobile navigation spacing
```

---

## Risk Mitigation

### Technical Risks

**Risk**: Unit conversion complexity
- **Mitigation**: Start with simple conversions, flag items that can't convert, accept manual adjustment

**Risk**: Price data freshness
- **Mitigation**: Show last updated date, manual entry for MVP, automate later

**Risk**: Mobile performance with large datasets
- **Mitigation**: Implement pagination, lazy loading, limit initial data fetch

### Scope Risks

**Risk**: Feature creep
- **Mitigation**: Strict adherence to MVP scope, defer all "nice-to-haves"

**Risk**: Over-engineering
- **Mitigation**: Simplest solution first, optimize only when needed

---

## Success Metrics Tracking

After 4 weeks of use (post-deployment):

**Primary Metrics**:
- [ ] Grocery spending reduced by 15-20% (compare to pre-app baseline)
- [ ] Meal planning time <30 min/week
- [ ] Using app every week for 4 consecutive weeks

**Secondary Metrics**:
- Number of recipes in library: Target 15-20
- Average meal plan completion rate: Target 80%+
- Actual vs planned cost variance: Target ±5%
- Preferred store identified: Yes/No

**Qualitative Assessment**:
- App feels faster than spreadsheet? Yes/No
- Would recommend to friend? Yes/No
- Most valuable feature? (open response)
- Biggest pain point? (open response)

---

## Handoff to Claude Code

This document package includes:
1. ✅ PROJECT-OVERVIEW.md - Project purpose and goals
2. ✅ DATABASE-SCHEMA.md - Complete schema with SQL
3. ✅ API-SPECIFICATION.md - All endpoints documented
4. ✅ UI-SPECIFICATIONS.md - Full UI designs and components
5. ✅ DEVELOPMENT-PLAN.md - Week-by-week implementation plan

**Next Steps**:
1. Review all documents
2. Ask clarifying questions
3. I'll create final REQUIREMENTS.md with user stories
4. Hand off to Claude Code for implementation

**Ready to begin implementation?**