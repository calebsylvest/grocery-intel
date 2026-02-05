# Requirements Specification

## Document Purpose
This document defines the functional and non-functional requirements for the Grocery Intelligence MVP using user stories, acceptance criteria, and detailed specifications.

---

## User Personas

### Primary Persona: Budget-Conscious Meal Planner (Caleb)
- **Demographics**: 30-45 years old, household manager, tech-savvy
- **Goals**: 
  - Reduce monthly grocery spending by 15-20%
  - Save time on meal planning (target <30 min/week)
  - Eliminate decision fatigue around "what's for dinner"
  - Track actual spending vs. budget
- **Pain Points**:
  - Don't know which store has best prices until comparing manually
  - Impulse purchases add unexpected costs
  - Forgetting ingredients leads to extra trips
  - Hard to see if meal choices fit budget until checkout
- **Technical Comfort**: High - comfortable with web apps, spreadsheets
- **Context**: Plans meals on weekend, shops 1-2x/week, uses loyalty cards

---

## Epic Overview

### Epic 1: Recipe Management
Manage personal recipe collection with ingredients and cost tracking.

### Epic 2: Multi-Store Price Tracking
Track and compare prices across multiple grocery stores.

### Epic 3: Meal Planning
Plan weekly meals using recipe library with cost visibility.

### Epic 4: Shopping List Generation
Generate aggregated shopping lists from meal plans with multi-store cost comparison.

### Epic 5: Spending Tracking
Record actual grocery spending and compare to planned budgets.

### Epic 6: Analytics & Insights
View historical spending patterns and identify savings opportunities.

---

## Epic 1: Recipe Management

### User Story 1.1: Create Recipe
**As a** meal planner  
**I want to** create a new recipe with ingredients and instructions  
**So that** I can reuse it in future meal plans

**Acceptance Criteria**:
- [ ] Can enter recipe name (required, max 255 chars)
- [ ] Can enter description (optional, text)
- [ ] Can set servings (required, integer > 0, default 4)
- [ ] Can set prep time in minutes (optional, integer)
- [ ] Can set cook time in minutes (optional, integer)
- [ ] Can select cuisine type from dropdown (optional)
- [ ] Can add ingredients with:
  - Food item (required, searchable dropdown)
  - Quantity (required, decimal)
  - Unit (required, dropdown)
  - Preparation notes (optional, e.g., "diced")
- [ ] Can add at least 1 ingredient (validation)
- [ ] Can add multiple ingredients (up to 50)
- [ ] Can remove ingredient rows
- [ ] Can enter instructions (optional, multi-line text)
- [ ] Can enter notes (optional)
- [ ] Form validates before submission
- [ ] Success message shown on save
- [ ] Redirects to recipe detail page after save
- [ ] Draft recovery if browser crashes (localStorage)

**Technical Notes**:
- Single transaction for recipe + ingredients insert
- If food item doesn't exist, show "Create New Food Item" option inline
- Auto-save draft every 30 seconds to localStorage

---

### User Story 1.2: View Recipe List
**As a** meal planner  
**I want to** browse my recipe collection  
**So that** I can find recipes to add to meal plans

**Acceptance Criteria**:
- [ ] Displays all recipes as cards/rows
- [ ] Shows recipe name, cuisine type, total time, cost per serving
- [ ] Can search by recipe name (client-side filter)
- [ ] Can filter by cuisine type (dropdown)
- [ ] Can sort by name, cost, time, date added
- [ ] Displays cost per serving if prices are available
- [ ] Shows "No price data" if costs can't be calculated
- [ ] Click recipe navigates to detail page
- [ ] "Add to Plan" quick action button
- [ ] Empty state if no recipes exist with "Create Recipe" CTA
- [ ] Loading state while fetching
- [ ] Mobile responsive (cards stack on small screens)

**Technical Notes**:
- Cost calculation happens server-side using latest prices
- If ingredient has no price, exclude from calculation or show "incomplete"
- Implement debounced search (wait 300ms after typing stops)

---

### User Story 1.3: View Recipe Detail
**As a** meal planner  
**I want to** view full recipe details including cost breakdown  
**So that** I can decide if it fits my budget

**Acceptance Criteria**:
- [ ] Displays recipe name, description, servings, times
- [ ] Shows full ingredient list with quantities and prep notes
- [ ] Displays step-by-step instructions
- [ ] Shows notes section
- [ ] Displays cost breakdown by store:
  - Total recipe cost per store
  - Cost per serving per store
  - Lowest cost highlighted in green
- [ ] If prices missing, shows "Price data incomplete" message
- [ ] "Edit" button navigates to edit form
- [ ] "Delete" button shows confirmation modal
- [ ] "Add to Meal Plan" button opens date picker modal
- [ ] Ingredients have checkboxes (for partial shopping lists - future)
- [ ] Mobile responsive layout

**Technical Notes**:
- Cost calculation uses latest price from prices table for each food item
- If multiple prices for same date, use one (arbitrary or average)
- Show last updated date for prices

---

### User Story 1.4: Edit Recipe
**As a** meal planner  
**I want to** update an existing recipe  
**So that** I can improve it or fix mistakes

**Acceptance Criteria**:
- [ ] Pre-fills form with existing recipe data
- [ ] Loads existing ingredients
- [ ] Can modify all fields (name, servings, ingredients, etc.)
- [ ] Can add new ingredients
- [ ] Can remove existing ingredients
- [ ] Removing ingredient doesn't immediately delete (only on save)
- [ ] Shows confirmation if navigating away with unsaved changes
- [ ] Saves to database on submit
- [ ] Success message on save
- [ ] Redirects to recipe detail page

**Technical Notes**:
- PUT request replaces all ingredients (delete old, insert new)
- Use transaction to ensure atomicity
- Consider soft delete for ingredients if needed for history

---

### User Story 1.5: Delete Recipe
**As a** meal planner  
**I want to** delete a recipe I no longer use  
**So that** my recipe list stays clean

**Acceptance Criteria**:
- [ ] Shows confirmation modal: "Are you sure you want to delete [Recipe Name]?"
- [ ] Explains deletion is permanent
- [ ] If recipe is in active meal plans, warns user
- [ ] Cancel button closes modal without action
- [ ] Confirm button deletes recipe and ingredients (cascade)
- [ ] Success message shown
- [ ] Navigates back to recipe list
- [ ] Recipe no longer appears in list

**Technical Notes**:
- CASCADE delete on recipe_ingredients
- RESTRICT delete if referenced in meal_plan_items (active plans)
- Could add "Archive" feature instead of hard delete (future)

---

## Epic 2: Multi-Store Price Tracking

### User Story 2.1: Add Store
**As a** meal planner  
**I want to** add a grocery store to track  
**So that** I can compare prices there

**Acceptance Criteria**:
- [ ] Can enter store name (required, max 255 chars)
- [ ] Can enter location/address (optional)
- [ ] Can select store type: grocery, warehouse, specialty, online (optional)
- [ ] Can enter website URL (optional)
- [ ] Can enter notes (hours, parking, etc.) (optional)
- [ ] Form validates before submission
- [ ] Success message on save
- [ ] New store appears in store list
- [ ] Store is active by default

**Technical Notes**:
- Set is_active = true on creation
- Store name doesn't need to be unique (multiple locations of same chain)

---

### User Story 2.2: View Stores
**As a** meal planner  
**I want to** see all my tracked stores  
**So that** I can manage them and view price data

**Acceptance Criteria**:
- [ ] Displays all active stores as cards
- [ ] Shows store name, location, type
- [ ] Shows count of items with prices entered
- [ ] Shows last price update date
- [ ] "Edit" button for each store
- [ ] "View Prices" button navigates to store price list
- [ ] "Deactivate" button soft deletes store
- [ ] Empty state if no stores with "Add Store" CTA
- [ ] Mobile responsive (cards stack)

**Technical Notes**:
- Price count query: SELECT COUNT(DISTINCT food_item_id) FROM prices WHERE store_id = ?
- Last update: SELECT MAX(date_recorded) FROM prices WHERE store_id = ?

---

### User Story 2.3: Enter Food Item Price
**As a** meal planner  
**I want to** record the price of a food item at a store  
**So that** cost calculations are accurate

**Acceptance Criteria**:
- [ ] Can select food item (required, searchable dropdown)
- [ ] Can select store (required, dropdown)
- [ ] Can enter price (required, decimal, > 0)
- [ ] Can enter unit (required, dropdown: lb, oz, each, gallon, etc.)
- [ ] Can enter package size description (optional, e.g., "2.5 lb bag")
- [ ] Can set date recorded (defaults to today)
- [ ] Can enter notes (optional, e.g., "on sale")
- [ ] Form validates before submission
- [ ] Success message on save
- [ ] New price shows in price list

**Technical Notes**:
- Creates new price record (doesn't update existing)
- Historical prices retained
- Consider bulk entry form (one store, multiple items)

---

### User Story 2.4: View Price List for Store
**As a** meal planner  
**I want to** see all prices I've recorded for a store  
**So that** I can update outdated prices

**Acceptance Criteria**:
- [ ] Displays store name in header
- [ ] Lists all food items with prices at this store
- [ ] Shows: food item name, price, unit, package size, last updated date
- [ ] Grouped by category (Meat, Dairy, Produce, etc.)
- [ ] Can search/filter by food item name
- [ ] Can filter by category
- [ ] Click item shows price history
- [ ] "Update Price" button opens price entry form (pre-filled)
- [ ] Empty state if no prices with "Add Price" CTA
- [ ] Mobile responsive

**Technical Notes**:
- Query latest price per food item for this store
- Sort by category, then food item name
- Price history modal shows all records ordered by date DESC

---

### User Story 2.5: View Price History
**As a** meal planner  
**I want to** see how a food item's price has changed over time  
**So that** I can identify trends and best times to buy

**Acceptance Criteria**:
- [ ] Shows food item name
- [ ] Displays table of price records: date, price, unit, store, notes
- [ ] Sorted by date descending (newest first)
- [ ] Shows price delta between records (e.g., "+$0.50" in red, "-$0.25" in green)
- [ ] Optional: Simple line chart showing price over time
- [ ] Can filter to specific store
- [ ] Covers last 30/60/90 days or all time (dropdown)
- [ ] Mobile responsive

**Technical Notes**:
- Query: SELECT * FROM prices WHERE food_item_id = ? ORDER BY date_recorded DESC
- Calculate delta: current_price - previous_price
- Chart library: Recharts (if implementing chart)

---

## Epic 3: Meal Planning

### User Story 3.1: Create Meal Plan
**As a** meal planner  
**I want to** create a new weekly meal plan  
**So that** I can organize meals for the upcoming week

**Acceptance Criteria**:
- [ ] Can enter plan name (optional, defaults to "Week of [start date]")
- [ ] Can select start date (required, date picker)
- [ ] Can select end date (optional, defaults to start + 6 days)
- [ ] Can enter notes (optional)
- [ ] Status automatically set to "planning"
- [ ] Form validates (end date >= start date)
- [ ] Success message on save
- [ ] Navigates to meal plan calendar view
- [ ] New plan appears in meal plans list

**Technical Notes**:
- Typical use case: 7-day plan (Mon-Sun)
- Allow custom date ranges for holidays, etc.
- Consider "Copy Last Week" feature (future)

---

### User Story 3.2: View Meal Plan Calendar
**As a** meal planner  
**I want to** see my meal plan in a calendar view  
**So that** I can visualize what I'm eating each day

**Acceptance Criteria**:
- [ ] Displays week in 7-column grid (desktop) or vertical list (mobile)
- [ ] Shows date and day name for each day
- [ ] Shows meal cards within each day slot
- [ ] Meal card displays: recipe name, meal type, servings, cost estimate
- [ ] Empty day shows "Add Meal" button
- [ ] Header shows total estimated cost for week
- [ ] Header shows "View Shopping List" button
- [ ] Can click meal card to edit (change servings, notes)
- [ ] Can remove meal from plan
- [ ] Loading state while fetching
- [ ] Mobile: Days stack vertically with meals under each

**Technical Notes**:
- Desktop: CSS Grid with 7 columns
- Mobile: Flexbox vertical stack
- Cost calculation server-side based on current prices
- Consider drag-and-drop reordering (future enhancement)

---

### User Story 3.3: Add Meal to Plan
**As a** meal planner  
**I want to** add a recipe to a specific day in my meal plan  
**So that** I can schedule what to cook

**Acceptance Criteria**:
- [ ] Click "Add Meal" opens modal/form
- [ ] Can search and select recipe (required)
- [ ] Can select meal type: breakfast, lunch, dinner, snack (required)
- [ ] Can set servings (defaults to recipe default, editable)
- [ ] Can enter notes (optional)
- [ ] Form validates before submission
- [ ] Success message on save
- [ ] Meal appears on calendar in correct day
- [ ] Calendar total cost updates

**Technical Notes**:
- POST to /api/meal-plans/:id/meals
- Re-fetch meal plan after adding to refresh costs
- Consider "Quick Add" from recipe list (future)

---

### User Story 3.4: Edit Meal in Plan
**As a** meal planner  
**I want to** change the servings or notes for a meal  
**So that** I can adjust portions or add reminders

**Acceptance Criteria**:
- [ ] Click meal card opens edit modal
- [ ] Shows current recipe, meal type, servings, notes
- [ ] Can change servings (integer > 0)
- [ ] Can edit notes
- [ ] Cannot change recipe (must delete and re-add)
- [ ] Form validates before submission
- [ ] Success message on save
- [ ] Meal card updates on calendar
- [ ] Total cost recalculates

**Technical Notes**:
- PUT to /api/meal-plan-items/:id
- Servings override affects cost calculation

---

### User Story 3.5: Remove Meal from Plan
**As a** meal planner  
**I want to** remove a meal I no longer want to cook  
**So that** my plan reflects actual intentions

**Acceptance Criteria**:
- [ ] Click meal card shows "Remove" button or icon
- [ ] Shows confirmation: "Remove [Recipe Name] from [Date]?"
- [ ] Cancel button closes without action
- [ ] Confirm removes meal from plan
- [ ] Success message shown
- [ ] Meal disappears from calendar
- [ ] Total cost updates
- [ ] Day shows "Add Meal" button again

**Technical Notes**:
- DELETE /api/meal-plan-items/:id
- No cascade (only deletes meal_plan_item record)

---

### User Story 3.6: View Meal Plans List
**As a** meal planner  
**I want to** see all my past and current meal plans  
**So that** I can review or reuse them

**Acceptance Criteria**:
- [ ] Displays meal plans as cards/rows
- [ ] Shows plan name, date range, status, meal count, estimated cost
- [ ] Can filter by status: planning, active, completed, archived
- [ ] Can filter by date range
- [ ] Sorted by start date descending (newest first)
- [ ] Click plan navigates to calendar view
- [ ] "Create New Plan" button at top
- [ ] Empty state if no plans with "Create Plan" CTA
- [ ] Mobile responsive

**Technical Notes**:
- GET /api/meal-plans with query params for filters
- Current week plan could be highlighted or pinned

---

## Epic 4: Shopping List Generation

### User Story 4.1: Generate Shopping List from Meal Plan
**As a** meal planner  
**I want to** create a shopping list from my meal plan  
**So that** I know exactly what to buy

**Acceptance Criteria**:
- [ ] Click "Shopping List" from meal plan view
- [ ] System aggregates all ingredients from all meals
- [ ] Combines duplicate ingredients (sums quantities)
- [ ] Groups items by category (Meat, Produce, etc.)
- [ ] Shows total quantity needed per item
- [ ] Lists which recipes use each ingredient
- [ ] Displays loading state (can take 1-2 seconds for calculations)
- [ ] Shows empty state if meal plan has no meals

**Technical Notes**:
- Backend aggregation logic:
  - Get all meals in plan
  - Get all ingredients for those recipes
  - Adjust quantities based on servings in meal plan
  - Group by food_item_id and sum quantities
  - Join with food_items for names and categories
- Handle unit mismatches (e.g., 1 cup + 8 oz = ?)
  - For MVP: Keep separate, show both
  - Future: Convert to common unit

---

### User Story 4.2: Compare Shopping List Cost Across Stores
**As a** meal planner  
**I want to** see how much my shopping list will cost at different stores  
**So that** I can choose the cheapest option

**Acceptance Criteria**:
- [ ] Displays cost summary cards for each store at top
- [ ] Shows store name and total cost
- [ ] Cheapest store highlighted in green with "BEST PRICE" badge
- [ ] Other stores show price delta (e.g., "+$6.69 more expensive")
- [ ] If item has no price at a store, shows "Price unavailable"
- [ ] If multiple items missing prices, shows warning
- [ ] Can click store card to filter list to that store's prices
- [ ] Mobile: Store cards horizontal scroll

**Technical Notes**:
- Fetch latest prices for all items at all stores
- Calculate: SUM(quantity * price_per_unit) per store
- Handle missing prices:
  - Option 1: Exclude item from total, note incomplete
  - Option 2: Use average price from other stores (estimates)
  - MVP: Option 1 (more honest)

---

### User Story 4.3: View Detailed Shopping List with Prices
**As a** meal planner  
**I want to** see each item's quantity and price  
**So that** I can verify the shopping list is correct

**Acceptance Criteria**:
- [ ] Lists all items grouped by category
- [ ] Category headers show category name + subtotal
- [ ] Each item shows:
  - Checkbox (for in-store tracking)
  - Food item name
  - Total quantity needed
  - Unit (lb, oz, each, etc.)
  - Unit price at selected store
  - Total cost for this item
  - "Used in" recipes (expandable or tooltip)
- [ ] Selected store dropdown at top changes prices shown
- [ ] Items with no price show "No price data" instead of amount
- [ ] Mobile responsive (simplified layout)

**Technical Notes**:
- Store selection in component state (doesn't persist)
- Checkbox state in localStorage (session only)
- Print stylesheet for paper shopping list (future)

---

### User Story 4.4: Mark Shopping as Complete
**As a** meal planner  
**I want to** record that I completed shopping  
**So that** I can track actual vs. planned spending

**Acceptance Criteria**:
- [ ] "Mark Trip Complete" button at bottom of shopping list
- [ ] Opens shopping trip entry modal
- [ ] Pre-fills store (current selected store)
- [ ] Pre-fills planned cost (from shopping list calculation)
- [ ] Pre-fills date (today)
- [ ] User enters actual cost from receipt (required)
- [ ] Auto-calculates variance
- [ ] User can add notes (optional)
- [ ] Saves trip to database
- [ ] Success message shown
- [ ] Navigates to dashboard or trip detail

**Technical Notes**:
- POST to /api/shopping-trips
- Links trip to meal plan (meal_plan_id)
- Updates meal plan status to "completed"? (optional)

---

## Epic 5: Spending Tracking

### User Story 5.1: Record Shopping Trip
**As a** meal planner  
**I want to** log my actual grocery spending  
**So that** I can compare to my budget

**Acceptance Criteria**:
- [ ] Can select which meal plan this trip is for (optional)
- [ ] Can select store (required)
- [ ] Can set trip date (required, defaults to today)
- [ ] Shows planned cost if linked to meal plan (read-only)
- [ ] Can enter actual cost (required, decimal > 0)
- [ ] Auto-calculates variance if planned cost exists
- [ ] Shows variance with color coding:
  - Green if under budget (negative variance)
  - Red if over budget (positive variance)
- [ ] Can enter notes (optional)
- [ ] Form validates before submission
- [ ] Success message on save
- [ ] Trip appears in recent trips list

**Technical Notes**:
- Variance = actual_cost - planned_cost
- Can record trips not linked to meal plans (ad-hoc shopping)
- Consider attaching receipt image (future)

---

### User Story 5.2: View Shopping Trips
**As a** meal planner  
**I want to** see my shopping trip history  
**So that** I can review spending patterns

**Acceptance Criteria**:
- [ ] Displays trips as cards/rows
- [ ] Shows trip date, store, actual cost, variance (if applicable)
- [ ] Can filter by:
  - Date range (last 7/30/90 days, custom)
  - Store
  - Meal plan
- [ ] Sorted by trip date descending (newest first)
- [ ] Click trip shows detail view
- [ ] Shows summary stats:
  - Total trips in period
  - Total spent
  - Average trip cost
  - Total saved vs. planned
- [ ] Empty state if no trips
- [ ] Mobile responsive

**Technical Notes**:
- GET /api/shopping-trips with query filters
- Summary stats calculated client-side or server-side

---

### User Story 5.3: Edit Shopping Trip
**As a** meal planner  
**I want to** correct a shopping trip if I entered wrong amount  
**So that** my records are accurate

**Acceptance Criteria**:
- [ ] Click trip shows "Edit" button
- [ ] Opens edit form pre-filled with trip data
- [ ] Can modify actual cost, notes
- [ ] Cannot change store or meal plan (must delete and recreate)
- [ ] Form validates before submission
- [ ] Success message on save
- [ ] Trip list updates

**Technical Notes**:
- PUT to /api/shopping-trips/:id
- Consider preventing edits after X days (data integrity)

---

### User Story 5.4: Delete Shopping Trip
**As a** meal planner  
**I want to** remove an incorrect trip entry  
**So that** my stats are accurate

**Acceptance Criteria**:
- [ ] Click trip shows "Delete" button
- [ ] Shows confirmation modal
- [ ] Cancel closes without action
- [ ] Confirm deletes trip
- [ ] Success message shown
- [ ] Trip removed from list

**Technical Notes**:
- DELETE /api/shopping-trips/:id
- Consider soft delete (is_deleted flag) for data integrity

---

## Epic 6: Analytics & Insights

### User Story 6.1: View Spending Dashboard
**As a** meal planner  
**I want to** see an overview of my spending  
**So that** I know if I'm meeting my budget goals

**Acceptance Criteria**:
- [ ] Shows current week meal plan summary:
  - Plan name
  - Number of meals
  - View plan link
- [ ] Shows current week spending summary:
  - Planned cost
  - Actual cost (if trip recorded)
  - Saved amount (green if saved, red if over)
  - Percentage saved
- [ ] Shows recent shopping trips (last 3-5)
- [ ] Shows quick stats (last 30 days):
  - Average weekly spend
  - Most shopped store (with percentage)
  - Average cost per meal
- [ ] All cards clickable (navigate to detail)
- [ ] Loading states for each section
- [ ] Mobile responsive

**Technical Notes**:
- Dashboard is home page (/)
- Fetch data from multiple endpoints
- Consider caching or combining into single endpoint (performance)

---

### User Story 6.2: View Spending Over Time
**As a** meal planner  
**I want to** see a chart of my grocery spending  
**So that** I can identify trends

**Acceptance Criteria**:
- [ ] Can select time period: 7/30/90 days, 6 months, 1 year
- [ ] Displays line or bar chart showing spending by week
- [ ] X-axis: Weeks or months
- [ ] Y-axis: Dollar amount
- [ ] Shows planned vs actual as separate lines/bars
- [ ] Tooltip on hover shows exact amounts
- [ ] Chart responsive (simplifies on mobile)
- [ ] Shows empty state if no data

**Technical Notes**:
- Use Recharts library
- Aggregate trips by week/month server-side
- Handle gaps (weeks with no trips)

---

### User Story 6.3: View Recipe Cost Ranking
**As a** meal planner  
**I want to** see which recipes are most expensive  
**So that** I can make budget-conscious choices

**Acceptance Criteria**:
- [ ] Shows two tables side-by-side:
  - Top 10 most expensive recipes (by cost per serving)
  - Top 10 most budget-friendly recipes
- [ ] Each row shows: recipe name, cost per serving, total cost (for default servings)
- [ ] Click recipe navigates to detail
- [ ] Shows "No price data" if costs can't be calculated
- [ ] Updates when prices change
- [ ] Mobile: Tables stack vertically

**Technical Notes**:
- GET /api/analytics/recipe-costs
- Calculate costs server-side using latest prices
- Cache for 1 hour (prices don't change that often)

---

### User Story 6.4: View Store Shopping Patterns
**As a** meal planner  
**I want to** see which stores I shop at most  
**So that** I can evaluate my store choices

**Acceptance Criteria**:
- [ ] Shows breakdown by store:
  - Store name
  - Number of trips
  - Total spent
  - Average trip cost
- [ ] Sorted by total spent descending
- [ ] Shows percentage of total spending per store
- [ ] Time period selector (same as other analytics)
- [ ] Empty state if no trips in period

**Technical Notes**:
- Query shopping_trips grouped by store_id
- Calculate percentages client-side

---

## Non-Functional Requirements

### Performance

**Page Load Time**
- [ ] Initial page load <3 seconds on 3G connection
- [ ] Subsequent navigation <1 second (SPA routing)
- [ ] API responses <500ms for simple queries
- [ ] API responses <2 seconds for complex calculations (shopping list)

**Optimization Strategies**:
- Code splitting by route
- Lazy load analytics charts
- Implement loading skeletons
- Cache GET requests (React Query or SWR)

---

### Usability

**Accessibility**
- [ ] WCAG 2.1 Level AA compliance
- [ ] Keyboard navigation for all interactive elements
- [ ] Screen reader compatible (semantic HTML, ARIA labels)
- [ ] Color contrast ratio ≥4.5:1 for text
- [ ] Touch targets ≥44x44px on mobile
- [ ] Focus indicators visible

**Mobile Responsiveness**
- [ ] Fully functional on screens 320px-1920px wide
- [ ] Touch-optimized interactions (no hover-only features)
- [ ] Thumb-friendly button placement
- [ ] Readable text without zooming (min 16px body text)

**Error Handling**
- [ ] All errors show user-friendly messages
- [ ] Form validation feedback inline
- [ ] Network errors show retry option
- [ ] No console errors in production
- [ ] Graceful degradation if JavaScript fails

---

### Security

**Data Protection**
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (React auto-escapes, sanitize user input)
- [ ] CORS configured for frontend domain only
- [ ] Environment variables for sensitive data
- [ ] No API keys or secrets in frontend code

**Note**: MVP is single-user with no authentication. If adding auth later:
- [ ] Password hashing (bcrypt)
- [ ] HTTPS only
- [ ] JWT or session-based auth
- [ ] CSRF protection

---

### Reliability

**Data Integrity**
- [ ] Database foreign key constraints enforced
- [ ] Transactions for multi-step operations (recipe + ingredients)
- [ ] Cascade deletes configured correctly
- [ ] No orphaned records

**Error Recovery**
- [ ] Form draft auto-save (localStorage)
- [ ] Confirm before navigating away from unsaved changes
- [ ] Database backups (manual for MVP)

---

### Maintainability

**Code Quality**
- [ ] Consistent naming conventions (camelCase JS, snake_case SQL)
- [ ] Components <300 lines (split if larger)
- [ ] Functions do one thing (single responsibility)
- [ ] No duplicate code (DRY principle)
- [ ] Comments for complex logic only

**Documentation**
- [ ] README with setup instructions
- [ ] API endpoints documented (this doc + inline comments)
- [ ] Database schema documented (DATABASE-SCHEMA.md)
- [ ] Component prop types documented (JSDoc or TypeScript)

---

### Browser Support

**Target Browsers**:
- [ ] Chrome/Edge (latest 2 versions)
- [ ] Firefox (latest 2 versions)
- [ ] Safari (latest 2 versions)
- [ ] Mobile Safari (iOS 14+)
- [ ] Chrome Mobile (Android 10+)

**Not Supporting**:
- Internet Explorer
- Browsers >2 years old

---

## Data Validation Rules

### Recipes
| Field | Validation |
|-------|------------|
| name | Required, 1-255 chars, unique recommended |
| servings | Required, integer, ≥1, ≤50 |
| prep_time | Optional, integer, ≥0, ≤999 minutes |
| cook_time | Optional, integer, ≥0, ≤999 minutes |
| ingredients | At least 1 required |

### Recipe Ingredients
| Field | Validation |
|-------|------------|
| food_item_id | Required, must exist |
| quantity | Required, decimal, >0, ≤9999 |
| unit | Required, max 50 chars |

### Food Items
| Field | Validation |
|-------|------------|
| name | Required, 1-255 chars, unique |
| category | Optional, max 100 chars |
| default_unit | Optional, max 50 chars, default "each" |

### Stores
| Field | Validation |
|-------|------------|
| name | Required, 1-255 chars |
| location | Optional, max 255 chars |
| website | Optional, valid URL format |

### Prices
| Field | Validation |
|-------|------------|
| food_item_id | Required, must exist |
| store_id | Required, must exist |
| price | Required, decimal, >0, ≤9999.99 |
| unit | Required, max 50 chars |
| date_recorded | Required, valid date, ≤today |

### Meal Plans
| Field | Validation |
|-------|------------|
| name | Optional, max 255 chars |
| start_date | Required, valid date |
| end_date | Required, valid date, ≥start_date |

### Meal Plan Items
| Field | Validation |
|-------|------------|
| meal_plan_id | Required, must exist |
| recipe_id | Required, must exist |
| meal_date | Required, valid date, within plan start-end |
| servings | Required, integer, ≥1, ≤50 |

### Shopping Trips
| Field | Validation |
|-------|------------|
| store_id | Required, must exist |
| trip_date | Required, valid date, ≤today |
| actual_cost | Required, decimal, >0, ≤9999.99 |

---

## Edge Cases & Business Rules

### Recipe Cost Calculation
- **Missing Prices**: If any ingredient has no price data, show "Incomplete cost data"
- **Multiple Prices Same Day**: Use any one (first in query result)
- **Servings Scaling**: Cost scales linearly with servings (e.g., 2x servings = 2x cost)
- **Rounding**: Display prices to 2 decimal places, calculate with higher precision

### Shopping List Aggregation
- **Duplicate Ingredients**: Sum quantities if same food_item_id
- **Unit Mismatch**: For MVP, keep separate (don't auto-convert)
  - Example: 1 cup milk + 8 oz milk → Show both
  - Future: Implement unit conversion table
- **Servings Adjustment**: Multiply ingredient quantity by (meal_servings / recipe_servings)
  - Example: Recipe for 4, meal plan has 6 → ingredients × 1.5

### Store Price Comparison
- **Incomplete Pricing**: Exclude items with no price from that store's total
  - Show warning: "2 items missing prices at Walmart"
- **Tie Breaker**: If two stores have same total, show both as "best price"
- **No Prices**: If no stores have any prices, show "Add prices to compare stores"

### Meal Plan Lifecycle
- **Status Transitions**: planning → active (manual or when trip recorded) → completed (manual or when end_date passes)
- **Overlapping Plans**: Allow (user might plan multiple weeks)
- **Past Plans**: Don't auto-delete, user can archive

### Shopping Trip Recording
- **Trip Without Plan**: Allow (for ad-hoc shopping trips)
- **Multiple Trips Per Plan**: Allow (some people shop multiple stores per week)
- **Future-Dated Trips**: Prevent (trip_date ≤ today)

---

## Success Metrics (Post-Launch)

### Primary Metrics
| Metric | Target | Measurement |
|--------|--------|-------------|
| Grocery spending reduction | 15-20% | Compare 4-week avg post-app to 4-week pre-app baseline |
| Time spent meal planning | <30 min/week | Self-reported + timestamp data |
| Weekly usage consistency | 12 consecutive weeks | Track meal plan creation dates |

### Secondary Metrics
| Metric | Target | Measurement |
|--------|--------|-------------|
| Recipes in library | 15-20 | Count recipes table |
| Meal plan completion rate | 80%+ | (Meals with trips) / (Total planned meals) |
| Planned vs actual variance | ±5% | AVG(ABS(variance / planned)) |
| Most shopped store identified | Yes | Majority (>50%) of trips at 1-2 stores |

### Qualitative Metrics
- App is faster than spreadsheet (yes/no)
- Would recommend to friend (yes/no)
- Most valuable feature (open response)
- Biggest pain point (open response)

**Measurement Schedule**:
- Week 4: Mid-point check-in
- Week 8: Final assessment
- Month 6: Long-term retention check

---

## Out of Scope (Explicitly NOT in MVP)

### Features Deferred to Future Versions
❌ User authentication / multi-user support  
❌ Recipe import from URLs (web scraping)  
❌ Automated price scraping  
❌ Nutritional tracking / calorie counting  
❌ Pantry inventory management  
❌ Barcode scanning  
❌ Receipt OCR  
❌ Mobile native apps (iOS/Android)  
❌ Recipe photos/images  
❌ Social features (sharing recipes)  
❌ Email notifications  
❌ Integration with grocery delivery APIs  
❌ Budget alerts/notifications  
❌ Recipe recommendations (AI)  
❌ Meal plan templates  
❌ Print/PDF export  
❌ Dark mode  
❌ Internationalization (i18n)  
❌ Advanced charts (pie, scatter, etc.)  

### Technical Decisions (Not Implementing)
❌ Real-time sync (WebSockets)  
❌ Offline mode (PWA caching)  
❌ Server-side rendering (SSR)  
❌ GraphQL API  
❌ Microservices architecture  
❌ Automated testing (unit/integration/e2e)  
❌ CI/CD pipeline  
❌ Load balancing / horizontal scaling  

**Rationale**: MVP focuses on core value proposition (meal planning + cost tracking). Above features add complexity without validating core hypothesis.

---

## Assumptions & Dependencies

### Assumptions
- User has 2-3 local grocery stores to track
- User will manually enter prices (at least initially)
- User shops 1-2 times per week
- User primarily eats dinner at home (meal planning focuses on dinners)
- Prices are relatively stable week-to-week (don't need daily updates)
- User is comfortable with web-based tools (no native app needed)
- Single user per instance (no household sharing needed yet)

### External Dependencies
- PostgreSQL database (Neon or local)
- Hosting provider (Railway/Render for backend, Vercel for frontend)
- Modern web browser with JavaScript enabled
- Stable internet connection (no offline mode)

### Internal Dependencies
- Price data manually entered by user
- Recipe library built by user
- Active maintenance (updating prices, creating meal plans)

---

## Glossary

**Food Item**: A generic ingredient that can be purchased (e.g., "Chicken Breast", "Milk")

**Product Variant**: (Not in MVP) A specific branded product (e.g., "Tyson Chicken Breast 2.5lb")

**Recipe**: A meal with ingredients and instructions

**Meal Plan**: A collection of recipes scheduled for specific dates

**Meal Plan Item**: A single meal scheduled within a meal plan

**Shopping List**: Aggregated list of ingredients from a meal plan

**Shopping Trip**: A recorded instance of grocery shopping with actual spending

**Store**: A grocery retailer where prices are tracked

**Price**: A recorded cost for a food item at a specific store on a specific date

**Cost per Serving**: Total recipe cost divided by number of servings

**Planned Cost**: Expected total based on current prices

**Actual Cost**: Real spending amount from receipt

**Variance**: Difference between actual and planned cost (negative = saved, positive = overspent)

---

## Acceptance Testing Checklist

Before considering MVP complete, verify:

### End-to-End Workflow
- [ ] Can create 10 recipes with ingredients
- [ ] Can add 3 stores
- [ ] Can enter prices for 20+ food items across stores
- [ ] Can create weekly meal plan
- [ ] Can add 7 meals to plan (one per day)
- [ ] Can view shopping list with multi-store comparison
- [ ] Can identify cheapest store
- [ ] Can record shopping trip with actual cost
- [ ] Can view dashboard with spending summary
- [ ] Can view analytics showing cost trends

### Data Integrity
- [ ] Recipe deletion cascades to ingredients
- [ ] Meal plan deletion cascades to meals
- [ ] Cannot delete food item referenced in recipes
- [ ] Cannot delete recipe in active meal plan
- [ ] Prices are retained historically
- [ ] Cost calculations use latest prices

### User Experience
- [ ] All forms validate properly
- [ ] Success/error messages display
- [ ] Loading states show during API calls
- [ ] Empty states guide user to action
- [ ] Mobile layout is usable
- [ ] No console errors
- [ ] All links/buttons work

### Performance
- [ ] Page loads in <3 seconds
- [ ] Shopping list generates in <2 seconds
- [ ] No lag when typing in forms
- [ ] Charts render smoothly

---

## Appendix: User Journey Map

### First-Time User Journey

**Day 1: Setup (30-45 minutes)**
1. Open app → Dashboard (empty state)
2. Add 3 stores (Kroger, Walmart, Whole Foods)
3. Create 5 favorite recipes with ingredients
4. Enter prices for those ingredients at 2 stores

**Day 2: First Meal Plan (20 minutes)**
1. Create new meal plan "Week of [next Monday]"
2. Add 5-7 recipes to plan
3. View shopping list
4. Compare store costs
5. Choose cheapest store

**Day 3: Shopping & Recording (5 minutes)**
1. Shop using generated list
2. Return home
3. Record shopping trip with actual cost
4. See variance (hopefully saved!)

**Ongoing (Weekly Cycle)**
1. Sunday: Plan next week (15 min)
2. Monday: Shop (use list)
3. Monday evening: Record trip (2 min)
4. Throughout week: Cook from plan
5. End of month: Review analytics (5 min)
