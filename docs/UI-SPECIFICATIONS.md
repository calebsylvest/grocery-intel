# UI Specifications

## Overview
Mobile-responsive web application built with React and Tailwind CSS. Design prioritizes clarity, speed, and cost visibility.

## Design Principles
1. **Cost-First**: Price information is prominent, not hidden
2. **Mobile-First**: Optimized for phone use (shopping context)
3. **Minimal Clicks**: Common tasks require ≤3 clicks
4. **Clear Hierarchy**: Primary actions visually distinct
5. **Data Density**: Show information efficiently without clutter

## Color Palette
```css
/* Primary Colors */
--primary-green: #10b981;      /* Success, savings */
--primary-blue: #3b82f6;       /* Actions, links */
--primary-red: #ef4444;        /* Over budget, warnings */

/* Neutral Colors */
--gray-50: #f9fafb;
--gray-100: #f3f4f6;
--gray-200: #e5e7eb;
--gray-500: #6b7280;
--gray-700: #374151;
--gray-900: #111827;

/* Semantic Colors */
--success: #10b981;
--warning: #f59e0b;
--error: #ef4444;
--info: #3b82f6;
```

## Typography
```css
/* Headings */
h1: text-3xl font-bold (30px)
h2: text-2xl font-semibold (24px)
h3: text-xl font-semibold (20px)
h4: text-lg font-medium (18px)

/* Body */
body: text-base (16px)
small: text-sm (14px)
tiny: text-xs (12px)

/* Font Family */
font-family: Inter, system-ui, sans-serif
```

## Layout Structure

### Desktop (≥1024px)
```
┌─────────────────────────────────────────┐
│ Header (Navigation)                     │
├──────────┬──────────────────────────────┤
│          │                              │
│ Sidebar  │  Main Content Area           │
│ (Nav)    │  (Dynamic based on route)    │
│          │                              │
│          │                              │
└──────────┴──────────────────────────────┘
```

### Mobile (<1024px)
```
┌─────────────────────┐
│ Header + Menu       │
├─────────────────────┤
│                     │
│                     │
│  Main Content       │
│  (Full width)       │
│                     │
│                     │
├─────────────────────┤
│ Bottom Nav (Fixed)  │
└─────────────────────┘
```

---

## Navigation

### Desktop Sidebar
```
┌─────────────────┐
│ Grocery Intel   │
├─────────────────┤
│ 🏠 Dashboard    │
│ 📋 Recipes      │
│ 🗓️  Meal Plans  │
│ 🛒 Shopping     │
│ 🏪 Stores       │
│ 📊 Analytics    │
└─────────────────┘
```

### Mobile Bottom Navigation
```
┌───────┬───────┬───────┬───────┬───────┐
│   📋  │  🗓️   │  🛒   │  🏪   │  ☰   │
│Recipe │ Plans │ Shop  │Stores │ More  │
└───────┴───────┴───────┴───────┴───────┘
```

Active route indicated by:
- Desktop: Blue left border + blue text
- Mobile: Blue icon + label

---

## Page Layouts

### 1. Dashboard (Home Page)

**Route**: `/`

**Purpose**: Quick overview of current meal plan and spending

**Layout**:
```
┌─────────────────────────────────────────┐
│ Dashboard                               │
├─────────────────────────────────────────┤
│                                         │
│ ┌─────────────────┐ ┌─────────────────┐│
│ │ This Week       │ │ Spending        ││
│ │                 │ │                 ││
│ │ Week of Feb 5   │ │ Planned: $87.50 ││
│ │ 7 meals planned │ │ Actual:  $84.23 ││
│ │                 │ │ Saved:   $3.27  ││
│ │ [View Plan →]   │ │                 ││
│ └─────────────────┘ └─────────────────┘│
│                                         │
│ ┌─────────────────────────────────────┐ │
│ │ Recent Shopping Trips               │ │
│ ├─────────────────────────────────────┤ │
│ │ Feb 5 - Kroger        $84.23        │ │
│ │ Jan 29 - Walmart      $91.12        │ │
│ │ Jan 22 - Kroger       $78.45        │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ ┌─────────────────────────────────────┐ │
│ │ Quick Stats (Last 30 Days)          │ │
│ ├─────────────────────────────────────┤ │
│ │ Average Weekly Spend: $82.60        │ │
│ │ Most Shopped Store: Kroger (75%)    │ │
│ │ Avg Cost Per Meal: $3.42            │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
```

**Components**:
- `WeeklySummaryCard`: Current active meal plan overview
- `SpendingSummaryCard`: Budget tracking card
- `RecentTripsTable`: Last 3-5 shopping trips
- `QuickStatsPanel`: Key metrics at a glance

**Actions**:
- "View Plan" → Navigate to current meal plan detail
- Click shopping trip → View trip details
- "Create New Plan" (if no active plan) → New meal plan flow

---

### 2. Recipes List

**Route**: `/recipes`

**Purpose**: Browse, search, and manage recipe library

**Layout**:
```
┌─────────────────────────────────────────┐
│ Recipes                    [+ New Recipe]│
├─────────────────────────────────────────┤
│ [Search...] [Filter: All ▾] [Sort: Name ▾]│
├─────────────────────────────────────────┤
│ ┌─────────────────────────────────────┐ │
│ │ Chicken Stir Fry        🍽️ 4 servings││
│ │ Asian · 35 min · $3.13/serving       ││
│ │ [View] [Add to Plan] [Edit]          ││
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ Spaghetti Bolognese     🍽️ 6 servings││
│ │ Italian · 45 min · $2.89/serving     ││
│ │ [View] [Add to Plan] [Edit]          ││
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
```

**Components**:
- `RecipeCard`: Individual recipe summary card
  - Title
  - Cuisine type · Total time · Cost per serving
  - Action buttons (View, Add to Plan, Edit)
- `SearchBar`: Filter by name
- `FilterDropdown`: Filter by cuisine, meal type
- `SortDropdown`: Sort by name, cost, time, recently added

**Recipe Card States**:
- Default: White background, gray border
- Hover: Light gray background, blue border
- Selected (when adding to plan): Blue background, white text

**Mobile Optimization**:
- Cards stack vertically full-width
- Action buttons horizontal scroll if needed
- Search bar sticky at top

---

### 3. Recipe Detail

**Route**: `/recipes/:id`

**Purpose**: View full recipe with cost breakdown

**Layout**:
```
┌─────────────────────────────────────────┐
│ ← Back to Recipes          [Edit] [Delete]│
├─────────────────────────────────────────┤
│ Chicken Stir Fry                        │
│ Quick and healthy weeknight dinner      │
│                                         │
│ 🍽️ 4 servings · ⏱️ 35 min · 🍜 Asian   │
├─────────────────────────────────────────┤
│ Ingredients               Cost Breakdown│
│ ┌─────────────────┐ ┌─────────────────┐│
│ │ □ 1.5 lb        │ │ Kroger: $12.48  ││
│ │ Chicken Breast  │ │ Walmart: $13.97 ││
│ │ (cubed)         │ │ Whole Foods: $17││
│ │                 │ │                 ││
│ │ □ 1 each        │ │ Per serving:    ││
│ │ Yellow Onion    │ │ Kroger: $3.12   ││
│ │ (sliced)        │ │                 ││
│ │                 │ │ [Add to Plan]   ││
│ │ □ 2 tbsp        │ │                 ││
│ │ Olive Oil       │ └─────────────────┘│
│ └─────────────────┘                    │
│                                         │
│ Instructions                            │
│ ┌─────────────────────────────────────┐ │
│ │ 1. Cut chicken into 1-inch cubes    │ │
│ │ 2. Heat oil in wok over high heat   │ │
│ │ 3. Cook chicken until golden...     │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ Notes                                   │
│ Use day-old rice for best results      │
└─────────────────────────────────────────┘
```

**Components**:
- `RecipeHeader`: Title, description, meta info
- `IngredientsList`: Checkboxes for shopping list creation
- `CostBreakdownPanel`: Multi-store cost comparison
  - Lowest price highlighted in green
  - Per-serving calculation prominent
- `InstructionsPanel`: Numbered steps
- `NotesPanel`: Tips and modifications

**Interactive Elements**:
- Checkboxes: Toggle ingredients for partial recipe use
- Store tabs: Switch between store price views
- "Add to Plan" button: Opens meal plan date picker modal

---

### 4. Recipe Create/Edit Form

**Route**: `/recipes/new` or `/recipes/:id/edit`

**Purpose**: Create new recipe or edit existing

**Layout**:
```
┌─────────────────────────────────────────┐
│ ← Cancel                    [Save Recipe]│
├─────────────────────────────────────────┤
│ New Recipe                              │
├─────────────────────────────────────────┤
│ Recipe Name *                           │
│ [________________________]              │
│                                         │
│ Description                             │
│ [________________________]              │
│                                         │
│ Servings *    Prep Time    Cook Time    │
│ [___4___]    [___15___]   [___20___]   │
│              minutes      minutes        │
│                                         │
│ Cuisine Type                            │
│ [Asian    ▾]                            │
│                                         │
│ ─── Ingredients ───        [+ Add]      │
│ ┌─────────────────────────────────────┐ │
│ │ [Chicken Breast ▾] [1.5] [lb ▾] [×] │ │
│ │ Prep notes: [cut into cubes_____]   │ │
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ [Yellow Onion  ▾] [1] [each ▾] [×]  │ │
│ │ Prep notes: [sliced_____________]   │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ ─── Instructions ───                    │
│ [                                     ] │
│ [                                     ] │
│ [                                     ] │
│                                         │
│ Notes                                   │
│ [                                     ] │
│                                         │
│              [Cancel] [Save Recipe]     │
└─────────────────────────────────────────┘
```

**Components**:
- `TextInput`: Recipe name, description
- `NumberInput`: Servings, prep time, cook time
- `Select`: Cuisine type
- `IngredientInputRow`: Repeatable component
  - Food item autocomplete dropdown
  - Quantity number input
  - Unit select dropdown
  - Remove button (×)
  - Preparation notes text input
- `TextArea`: Instructions (multi-line)
- `TextArea`: Notes

**Validation**:
- Name: Required, max 255 chars
- Servings: Required, integer > 0
- At least 1 ingredient required
- Ingredient quantity: Required, > 0
- Ingredient unit: Required

**Form Behavior**:
- Auto-save to localStorage every 30s (draft recovery)
- "Add Ingredient" button: Adds new ingredient row
- Remove (×) button: Removes ingredient row
- Cancel: Confirm if unsaved changes

**Mobile Optimization**:
- Stack all inputs vertically
- Full-width inputs
- Ingredient rows stack components vertically

---

### 5. Meal Plan Calendar

**Route**: `/meal-plans/:id`

**Purpose**: View/edit weekly meal plan

**Layout** (Desktop):
```
┌─────────────────────────────────────────┐
│ Week of Feb 5        [Shopping List] [Edit]│
├─────────────────────────────────────────┤
│ Feb 5-11, 2025 · Status: Planning       │
│ Estimated Cost: $87.50                  │
├───────┬───────┬───────┬───────┬───────┬─┤
│ Mon 5 │ Tue 6 │ Wed 7 │ Thu 8 │ Fri 9 │…│
├───────┼───────┼───────┼───────┼───────┼─┤
│┌─────┐│┌─────┐│┌─────┐│       │┌─────┐│ │
││Chick││Spagh││Chick││  +    ││Tacos││ │
││Stir ││etti  ││Tacos││ Add   ││     ││ │
││Fry  ││      ││     ││ Meal  ││     ││ │
││     ││      ││     ││       ││     ││ │
││$12  ││$15   ││$11   ││       ││$13  ││ │
│└─────┘│└─────┘│└─────┘│       │└─────┘│ │
└───────┴───────┴───────┴───────┴───────┴─┘
```

**Layout** (Mobile):
```
┌─────────────────────────┐
│ Week of Feb 5     [☰]  │
├─────────────────────────┤
│ Estimated: $87.50       │
│ [View Shopping List]    │
├─────────────────────────┤
│ Monday, Feb 5           │
│ ┌─────────────────────┐ │
│ │ Chicken Stir Fry    │ │
│ │ Dinner · 4 servings │ │
│ │ $12.50              │ │
│ │ [Edit] [Remove]     │ │
│ └─────────────────────┘ │
├─────────────────────────┤
│ Tuesday, Feb 6          │
│ ┌─────────────────────┐ │
│ │ Spaghetti Bolognese │ │
│ │ Dinner · 6 servings │ │
│ │ $15.75              │ │
│ │ [Edit] [Remove]     │ │
│ └─────────────────────┘ │
├─────────────────────────┤
│ Wednesday, Feb 7        │
│ [+ Add Meal]            │
└─────────────────────────┘
```

**Components**:
- `MealPlanHeader`: Title, date range, status, cost summary
- `CalendarGrid` (Desktop): 7-column grid for week
- `MealCardDay` (Mobile): Vertical list by day
- `MealCard`: Individual meal within day
  - Recipe name
  - Meal type (breakfast/lunch/dinner)
  - Servings
  - Cost
  - Edit/Remove actions
- `AddMealButton`: Trigger add meal modal

**Interactions**:
- Click meal card: Edit meal details (servings, notes)
- Click "+ Add Meal" or empty day: Open meal selection modal
- Drag & drop (desktop only): Reorder meals between days
- "Shopping List" button: Navigate to shopping list view

**Add Meal Modal**:
```
┌─────────────────────────┐
│ Add Meal to Feb 5     × │
├─────────────────────────┤
│ Select Recipe           │
│ [Search recipes...    ] │
│                         │
│ ○ Chicken Stir Fry      │
│ ○ Spaghetti Bolognese   │
│ ○ Beef Tacos            │
│                         │
│ Meal Type               │
│ [Dinner      ▾]         │
│                         │
│ Servings                │
│ [___4___]               │
│                         │
│       [Cancel] [Add]    │
└─────────────────────────┘
```

---

### 6. Shopping List

**Route**: `/meal-plans/:id/shopping-list`

**Purpose**: View aggregated shopping list with multi-store cost comparison

**Layout**:
```
┌─────────────────────────────────────────┐
│ Shopping List - Week of Feb 5           │
│ ← Back to Meal Plan                     │
├─────────────────────────────────────────┤
│ Store Comparison                        │
│ ┌──────────┬──────────┬──────────┐     │
│ │ Kroger   │ Walmart  │ Whole Fd │     │
│ │ $87.43   │ $94.12   │ $112.87  │     │
│ │ CHEAPEST │ +$6.69   │ +$25.44  │     │
│ └──────────┴──────────┴──────────┘     │
│                                         │
│ Shopping at: [Kroger ▾]    Total: $87.43│
├─────────────────────────────────────────┤
│ Meat (3 items)                    $32.15│
│ ┌─────────────────────────────────────┐ │
│ │ □ Chicken Breast Boneless           │ │
│ │   3.0 lb @ $3.99/lb = $11.97        │ │
│ │   Used in: Stir Fry, Tacos          │ │
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ □ Ground Beef 80/20                 │ │
│ │   2.0 lb @ $4.99/lb = $9.98         │ │
│ │   Used in: Spaghetti                │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ Produce (5 items)                 $8.42 │
│ ┌─────────────────────────────────────┐ │
│ │ □ Yellow Onion                      │ │
│ │   2.5 lb @ $0.99/lb = $2.48         │ │
│ │   Used in: Stir Fry, Spaghetti, ... │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ [Print List] [Mark Trip Complete]      │
└─────────────────────────────────────────┘
```

**Components**:
- `StoreCostComparison`: Cards showing total per store
  - Cheapest highlighted in green
  - Price delta shown for others
- `StoreSelector`: Dropdown to change active store view
- `TotalCostDisplay`: Large, prominent total for selected store
- `CategorySection`: Collapsible sections by food category
  - Header shows category name + subtotal
- `ShoppingListItem`: Individual item
  - Checkbox for in-store tracking
  - Item name
  - Quantity calculation with unit price
  - "Used in" recipes list
- `ActionButtons`: Print, export, mark complete

**Interactions**:
- Click store card: Switch to that store's pricing
- Toggle checkbox: Track items as purchased
- Click item: Expand to show price history, alternatives
- "Mark Trip Complete": Opens shopping trip entry form

**Mobile Optimization**:
- Store cards horizontal scroll
- Single column layout
- Sticky total at top
- Checkboxes large for thumb tapping

---

### 7. Shopping Trip Entry

**Route**: `/shopping-trips/new` (Modal or dedicated page)

**Purpose**: Record actual shopping results

**Layout**:
```
┌─────────────────────────┐
│ Record Shopping Trip  × │
├─────────────────────────┤
│ For: Week of Feb 5      │
│                         │
│ Store *                 │
│ [Kroger - Main St ▾]    │
│                         │
│ Date *                  │
│ [Feb 5, 2025]           │
│                         │
│ Planned Cost            │
│ $87.50 (read-only)      │
│                         │
│ Actual Cost *           │
│ $[_84.23__]             │
│                         │
│ Difference              │
│ -$3.27 (3.74% saved) ✓  │
│                         │
│ Notes                   │
│ [Onions on sale,      ] │
│ [chicken thighs       ] │
│ [substituted          ] │
│                         │
│    [Cancel] [Save Trip] │
└─────────────────────────┘
```

**Components**:
- `StoreSelect`: Dropdown of active stores
- `DatePicker`: Calendar for trip date
- `PlannedCostDisplay`: Read-only, from shopping list
- `ActualCostInput`: Number input with $ prefix
- `VarianceDisplay`: Auto-calculated
  - Negative (saved): Green text with ✓
  - Positive (over): Red text with ⚠
  - Percentage shown
- `NotesTextArea`: Free-form notes

**Validation**:
- Store: Required
- Date: Required, cannot be future date
- Actual cost: Required, must be > 0

**Success State**:
- Show success message
- Navigate to shopping trips list
- Update dashboard stats

---

### 8. Stores Management

**Route**: `/stores`

**Purpose**: Manage store list and view price data

**Layout**:
```
┌─────────────────────────────────────────┐
│ Stores                     [+ Add Store] │
├─────────────────────────────────────────┤
│ ┌─────────────────────────────────────┐ │
│ │ Kroger - Main St                    │ │
│ │ 123 Main St, Dallas TX              │ │
│ │ Grocery · 42 items priced           │ │
│ │ Last updated: Feb 4, 2025           │ │
│ │ [Edit] [View Prices] [Deactivate]   │ │
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ Walmart Supercenter                 │ │
│ │ 456 Highway 75, Dallas TX           │ │
│ │ Warehouse · 38 items priced         │ │
│ │ Last updated: Feb 3, 2025           │ │
│ │ [Edit] [View Prices] [Deactivate]   │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
```

**Components**:
- `StoreCard`: Individual store summary
  - Name and location
  - Store type
  - Price data stats (count, last update)
  - Action buttons
- `AddStoreButton`: Trigger add store modal

**Store Detail View** (`/stores/:id/prices`):
```
┌─────────────────────────────────────────┐
│ ← Back    Kroger - Main St        [Edit]│
├─────────────────────────────────────────┤
│ Prices (42 items)         [+ Add Price] │
│                                         │
│ [Search items...] [Category: All ▾]     │
├─────────────────────────────────────────┤
│ Chicken Breast Boneless          $3.99/lb│
│ Last updated: Feb 4, 2025                │
│ [Update Price] [View History]           │
├─────────────────────────────────────────┤
│ Ground Beef 80/20                $4.99/lb│
│ Last updated: Feb 3, 2025                │
│ [Update Price] [View History]           │
└─────────────────────────────────────────┘
```

---

### 9. Analytics Dashboard

**Route**: `/analytics`

**Purpose**: Historical spending analysis and trends

**Layout**:
```
┌─────────────────────────────────────────┐
│ Analytics                               │
├─────────────────────────────────────────┤
│ Time Period: [Last 30 Days ▾]           │
├─────────────────────────────────────────┤
│ Spending Over Time                      │
│ ┌─────────────────────────────────────┐ │
│ │     ▆                               │ │
│ │   ▆ █                               │ │
│ │ ▆ █ █ ▆                             │ │
│ │ █ █ █ █ ▆ ▆                         │ │
│ │ Wk1 Wk2 Wk3 Wk4                     │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ Top Expensive Recipes                   │
│ ┌─────────────────────────────────────┐ │
│ │ 1. Salmon Dinner      $6.89/serving │ │
│ │ 2. Steak Fajitas      $5.42/serving │ │
│ │ 3. Shrimp Pasta       $4.98/serving │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ Most Budget-Friendly Recipes            │
│ ┌─────────────────────────────────────┐ │
│ │ 1. Bean Chili         $1.89/serving │ │
│ │ 2. Pasta Marinara     $2.12/serving │ │
│ │ 3. Rice & Beans       $2.34/serving │ │
│ └─────────────────────────────────────┘ │
│                                         │
│ Store Comparison                        │
│ ┌─────────────────────────────────────┐ │
│ │ Kroger: 12 trips, avg $82.15        │ │
│ │ Walmart: 4 trips, avg $89.43        │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
```

**Components**:
- `TimePeriodSelector`: Dropdown (Last 7 days, 30 days, 3 months, Year)
- `SpendingChart`: Line or bar chart showing weekly spend
- `TopRecipesTable`: Ranked by cost per serving
- `StoreStatsPanel`: Comparison of shopping patterns

**Chart Library**: Recharts (lightweight React chart library)

---

## Common Components

### Buttons

**Primary Button**
```jsx
<button className="bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded">
  Save
</button>
```

**Secondary Button**
```jsx
<button className="bg-gray-200 hover:bg-gray-300 text-gray-800 font-medium py-2 px-4 rounded">
  Cancel
</button>
```

**Danger Button**
```jsx
<button className="bg-red-600 hover:bg-red-700 text-white font-medium py-2 px-4 rounded">
  Delete
</button>
```

**Icon Button**
```jsx
<button className="p-2 hover:bg-gray-100 rounded-full">
  <PencilIcon className="h-5 w-5" />
</button>
```

---

### Cards

**Standard Card**
```jsx
<div className="bg-white border border-gray-200 rounded-lg p-4 shadow-sm">
  {/* Card content */}
</div>
```

**Hoverable Card**
```jsx
<div className="bg-white border border-gray-200 rounded-lg p-4 shadow-sm 
                hover:border-blue-500 hover:shadow-md transition-all cursor-pointer">
  {/* Card content */}
</div>
```

---

### Forms

**Text Input**
```jsx
<div className="mb-4">
  <label className="block text-sm font-medium text-gray-700 mb-1">
    Recipe Name *
  </label>
  <input 
    type="text"
    className="w-full border border-gray-300 rounded px-3 py-2 
               focus:outline-none focus:ring-2 focus:ring-blue-500"
    placeholder="Enter recipe name"
  />
</div>
```

**Select Dropdown**
```jsx
<select className="w-full border border-gray-300 rounded px-3 py-2 
                   focus:outline-none focus:ring-2 focus:ring-blue-500">
  <option>Select option</option>
  <option>Option 1</option>
</select>
```

**Number Input**
```jsx
<input 
  type="number"
  min="0"
  step="0.01"
  className="w-20 border border-gray-300 rounded px-3 py-2 text-right
             focus:outline-none focus:ring-2 focus:ring-blue-500"
/>
```

---

### Modals

**Modal Structure**
```jsx
<div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
  <div className="bg-white rounded-lg max-w-md w-full mx-4 max-h-[90vh] overflow-y-auto">
    {/* Modal Header */}
    <div className="flex items-center justify-between p-4 border-b">
      <h3 className="text-lg font-semibold">Modal Title</h3>
      <button className="text-gray-400 hover:text-gray-600">
        <XIcon className="h-5 w-5" />
      </button>
    </div>
    
    {/* Modal Body */}
    <div className="p-4">
      {/* Modal content */}
    </div>
    
    {/* Modal Footer */}
    <div className="flex justify-end gap-2 p-4 border-t">
      <button className="secondary-btn">Cancel</button>
      <button className="primary-btn">Confirm</button>
    </div>
  </div>
</div>
```

---

### Loading States

**Spinner**
```jsx
<div className="flex items-center justify-center p-8">
  <div className="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
</div>
```

**Skeleton Loading**
```jsx
<div className="animate-pulse">
  <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
  <div className="h-4 bg-gray-200 rounded w-1/2"></div>
</div>
```

---

### Empty States

**No Data**
```jsx
<div className="text-center py-12">
  <svg className="mx-auto h-12 w-12 text-gray-400" /* icon */ />
  <h3 className="mt-2 text-sm font-medium text-gray-900">No recipes yet</h3>
  <p className="mt-1 text-sm text-gray-500">Get started by creating a new recipe.</p>
  <button className="mt-4 primary-btn">
    + New Recipe
  </button>
</div>
```

---

### Toast Notifications

**Success Toast**
```jsx
<div className="fixed top-4 right-4 bg-green-50 border border-green-200 rounded-lg p-4 shadow-lg">
  <div className="flex items-center">
    <CheckCircleIcon className="h-5 w-5 text-green-600 mr-2" />
    <span className="text-green-800">Recipe saved successfully!</span>
  </div>
</div>
```

**Error Toast**
```jsx
<div className="fixed top-4 right-4 bg-red-50 border border-red-200 rounded-lg p-4 shadow-lg">
  <div className="flex items-center">
    <XCircleIcon className="h-5 w-5 text-red-600 mr-2" />
    <span className="text-red-800">Failed to save recipe. Please try again.</span>
  </div>
</div>
```

---

## Responsive Breakpoints
```css
/* Mobile */
@media (max-width: 640px) { /* sm */ }

/* Tablet */
@media (min-width: 641px) and (max-width: 1023px) { /* md */ }

/* Desktop */
@media (min-width: 1024px) { /* lg */ }
```

**Mobile-Specific Adjustments**:
- Sidebar → Bottom navigation
- Multi-column grids → Single column
- Side-by-side forms → Stacked
- Smaller font sizes for headings
- Larger tap targets (min 44x44px)
- Reduced padding/margins

---

## Accessibility Requirements

1. **Keyboard Navigation**
   - All interactive elements accessible via Tab
   - Visible focus indicators (blue outline)
   - Skip to main content link

2. **Screen Reader Support**
   - Semantic HTML (header, nav, main, section)
   - ARIA labels for icon buttons
   - Form labels properly associated
   - Error messages announced

3. **Color Contrast**
   - Text meets WCAG AA standards (4.5:1 minimum)
   - Don't rely solely on color for meaning
   - Provide text alternatives for icons

4. **Touch Targets**
   - Minimum 44x44px for mobile
   - Adequate spacing between clickable elements

---

## Icon Library

Use **Heroicons** (MIT license, integrates well with Tailwind)

Common icons needed:
- Home (dashboard)
- Document (recipes)
- Calendar (meal plans)
- ShoppingCart (shopping lists)
- Store (stores)
- ChartBar (analytics)
- Plus (add)
- Pencil (edit)
- Trash (delete)
- X (close)
- Check (success)
- ExclamationCircle (warning)
- InformationCircle (info)

---

## Performance Considerations

1. **Lazy Loading**
   - Code split by route
   - Load analytics charts only when needed

2. **Image Optimization**
   - None in MVP (no recipe images)
   - Future: Use Next.js Image component

3. **Data Fetching**
   - Implement loading states for all async operations
   - Cache GET requests (React Query recommended)
   - Optimistic updates for fast UX

4. **Bundle Size**
   - Keep dependencies minimal
   - Use tree-shaking compatible libraries
   - Lazy load heavy components (charts)

---

## Implementation Priority

**Phase 1** (Weeks 1-2):
- Basic layout structure
- Navigation (sidebar + mobile bottom nav)
- Recipe list + detail views
- Recipe create/edit form

**Phase 2** (Weeks 3-4):
- Meal plan calendar (desktop + mobile)
- Add meal modal
- Shopping list view
- Store cost comparison

**Phase 3** (Weeks 5-6):
- Shopping trip entry
- Dashboard with summary cards
- Stores management
- Basic analytics charts

**Polish** (Week 7-8):
- Loading states
- Empty states
- Error handling
- Toast notifications
- Mobile optimization refinement