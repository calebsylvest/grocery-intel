# Database Schema

## Overview
PostgreSQL relational database designed for single-user grocery and meal planning with multi-store price tracking.

## Entity Relationship Diagram (Text Format)
```
food_items (1) ──< (many) recipe_ingredients >── (many) recipes
food_items (1) ──< (many) prices >── (many) stores
recipes (1) ──< (many) meal_plan_items >── (many) meal_plans
meal_plans (1) ──< (many) shopping_trips >── (many) stores
```

## Table Definitions

### 1. food_items
Core ingredients/products that can be purchased.
```sql
CREATE TABLE food_items (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL UNIQUE,
  category VARCHAR(100),
  default_unit VARCHAR(50) DEFAULT 'each',
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_food_items_category ON food_items(category);
CREATE INDEX idx_food_items_name ON food_items(name);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `name`: Display name (e.g., "Chicken Breast", "Whole Milk", "Russet Potatoes")
- `category`: Group classification for organization (e.g., "Meat", "Dairy", "Produce", "Pantry", "Frozen")
- `default_unit`: Standard unit of measure (lb, oz, cup, each, gallon, package)
- `notes`: Additional context or storage instructions
- `created_at`, `updated_at`: Audit timestamps

**Sample Data**:
```sql
INSERT INTO food_items (name, category, default_unit) VALUES
  ('Chicken Breast Boneless', 'Meat', 'lb'),
  ('Whole Milk', 'Dairy', 'gallon'),
  ('Eggs Large', 'Dairy', 'dozen'),
  ('Yellow Onion', 'Produce', 'lb'),
  ('Olive Oil', 'Pantry', 'oz');
```

---

### 2. recipes
Meals that can be prepared, composed of food_items.
```sql
CREATE TABLE recipes (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  description TEXT,
  servings INTEGER DEFAULT 4,
  prep_time INTEGER, -- minutes
  cook_time INTEGER, -- minutes
  instructions TEXT,
  cuisine_type VARCHAR(100),
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_recipes_name ON recipes(name);
CREATE INDEX idx_recipes_cuisine ON recipes(cuisine_type);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `name`: Recipe title (e.g., "Chicken Stir Fry", "Spaghetti Bolognese")
- `description`: Short summary or appeal description
- `servings`: Default portion count (scalable in meal plans)
- `prep_time`: Preparation time in minutes
- `cook_time`: Active cooking time in minutes
- `instructions`: Step-by-step cooking directions
- `cuisine_type`: Optional classification (Italian, Mexican, Asian, American)
- `notes`: Tips, substitutions, personal modifications

**Sample Data**:
```sql
INSERT INTO recipes (name, servings, prep_time, cook_time, instructions) VALUES
  ('Chicken Stir Fry', 4, 15, 20, '1. Cut chicken into cubes\n2. Heat oil in wok...');
```

---

### 3. recipe_ingredients
Junction table linking recipes to food_items with quantities.
```sql
CREATE TABLE recipe_ingredients (
  id SERIAL PRIMARY KEY,
  recipe_id INTEGER NOT NULL REFERENCES recipes(id) ON DELETE CASCADE,
  food_item_id INTEGER NOT NULL REFERENCES food_items(id) ON DELETE RESTRICT,
  quantity DECIMAL(10,2) NOT NULL,
  unit VARCHAR(50) NOT NULL,
  preparation_notes VARCHAR(255),
  optional BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_recipe_ingredients_recipe ON recipe_ingredients(recipe_id);
CREATE INDEX idx_recipe_ingredients_food ON recipe_ingredients(food_item_id);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `recipe_id`: Foreign key to recipes
- `food_item_id`: Foreign key to food_items
- `quantity`: Numeric amount (e.g., 1.5, 2, 0.25)
- `unit`: Unit of measure (lb, oz, cup, tbsp, tsp, each)
- `preparation_notes`: How to prepare (e.g., "diced", "chopped", "minced")
- `optional`: Flag for garnishes or optional ingredients
- Cascade delete when recipe deleted; restrict when food_item has references

**Sample Data**:
```sql
INSERT INTO recipe_ingredients (recipe_id, food_item_id, quantity, unit, preparation_notes) VALUES
  (1, 1, 1.5, 'lb', 'cut into cubes'),
  (1, 4, 0.5, 'each', 'sliced'),
  (1, 5, 2, 'tbsp', '');
```

---

### 4. stores
Physical or online grocery retailers.
```sql
CREATE TABLE stores (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  location VARCHAR(255),
  store_type VARCHAR(100) DEFAULT 'grocery',
  website VARCHAR(255),
  notes TEXT,
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_stores_name ON stores(name);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `name`: Store name (e.g., "Kroger", "Walmart Supercenter", "Whole Foods")
- `location`: Address or neighborhood identifier
- `store_type`: Classification (grocery, warehouse, specialty, online)
- `website`: URL for online ordering or store info
- `notes`: Hours, parking, personal preferences
- `is_active`: Soft delete flag for stores no longer used

**Sample Data**:
```sql
INSERT INTO stores (name, location, store_type) VALUES
  ('Kroger', 'Local Kroger store', 'grocery'),
  ('Walmart', 'Local Walmart Supercenter', 'warehouse'),
  ('Whole Foods', 'Local Whole Foods Market', 'specialty');
```

---

### 5. prices
Historical pricing data for food_items at specific stores.
```sql
CREATE TABLE prices (
  id SERIAL PRIMARY KEY,
  food_item_id INTEGER NOT NULL REFERENCES food_items(id) ON DELETE CASCADE,
  store_id INTEGER NOT NULL REFERENCES stores(id) ON DELETE CASCADE,
  price DECIMAL(10,2) NOT NULL,
  unit VARCHAR(50) NOT NULL,
  package_size VARCHAR(100),
  date_recorded DATE DEFAULT CURRENT_DATE,
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_prices_food_item ON prices(food_item_id);
CREATE INDEX idx_prices_store ON prices(store_id);
CREATE INDEX idx_prices_date ON prices(date_recorded);
CREATE UNIQUE INDEX idx_prices_latest ON prices(food_item_id, store_id, date_recorded);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `food_item_id`: Foreign key to food_items
- `store_id`: Foreign key to stores
- `price`: Dollar amount (e.g., 3.99, 12.50)
- `unit`: Unit pricing basis (per lb, per oz, per package, each)
- `package_size`: Package description (e.g., "2.5 lb package", "16 oz bottle")
- `date_recorded`: When price was observed/entered
- `notes`: Sale status, unit price conversions, quality observations

**Sample Data**:
```sql
-- Store IDs: 1=Kroger, 2=Walmart, 3=Whole Foods
INSERT INTO prices (food_item_id, store_id, price, unit, package_size, date_recorded) VALUES
  (1, 1, 3.99, 'lb', 'loose', CURRENT_DATE),  -- Chicken at Kroger
  (1, 2, 3.49, 'lb', 'loose', CURRENT_DATE),  -- Chicken at Walmart
  (1, 3, 6.99, 'lb', 'loose', CURRENT_DATE),  -- Chicken at Whole Foods
  (2, 1, 3.29, 'gallon', '1 gallon jug', CURRENT_DATE);  -- Milk at Kroger
```

**Business Logic**:
- Latest price per food_item + store = most recent date_recorded entry
- Historical prices retained for trend analysis
- Unique constraint prevents duplicate prices for same item/store/date

---

### 6. meal_plans
Weekly or custom-period meal planning containers.
```sql
CREATE TABLE meal_plans (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) DEFAULT 'Weekly Plan',
  start_date DATE NOT NULL,
  end_date DATE NOT NULL,
  status VARCHAR(50) DEFAULT 'planning',
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_meal_plans_dates ON meal_plans(start_date, end_date);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `name`: User-defined label (e.g., "Week of Feb 5", "Holiday Week")
- `start_date`: First day of plan period
- `end_date`: Last day of plan period
- `status`: Workflow state (planning, active, completed, archived)
- `notes`: Goals, dietary restrictions for this period, special events

**Sample Data**:
```sql
INSERT INTO meal_plans (name, start_date, end_date) VALUES
  ('Week of Feb 5', '2025-02-05', '2025-02-11');
```

---

### 7. meal_plan_items
Individual meal assignments within a meal plan.
```sql
CREATE TABLE meal_plan_items (
  id SERIAL PRIMARY KEY,
  meal_plan_id INTEGER NOT NULL REFERENCES meal_plans(id) ON DELETE CASCADE,
  recipe_id INTEGER NOT NULL REFERENCES recipes(id) ON DELETE RESTRICT,
  meal_date DATE NOT NULL,
  meal_type VARCHAR(50) DEFAULT 'dinner',
  servings INTEGER DEFAULT 4,
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_meal_plan_items_plan ON meal_plan_items(meal_plan_id);
CREATE INDEX idx_meal_plan_items_date ON meal_plan_items(meal_date);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `meal_plan_id`: Foreign key to meal_plans
- `recipe_id`: Foreign key to recipes
- `meal_date`: Specific day this meal is scheduled
- `meal_type`: Category (breakfast, lunch, dinner, snack)
- `servings`: Override default recipe servings if needed
- `notes`: Adjustments, who's eating, leftovers plan

**Sample Data**:
```sql
INSERT INTO meal_plan_items (meal_plan_id, recipe_id, meal_date, meal_type, servings) VALUES
  (1, 1, '2025-02-05', 'dinner', 4),
  (1, 1, '2025-02-06', 'dinner', 4);
```

---

### 8. shopping_trips
Tracking actual grocery purchases against meal plans.
```sql
CREATE TABLE shopping_trips (
  id SERIAL PRIMARY KEY,
  meal_plan_id INTEGER REFERENCES meal_plans(id) ON DELETE SET NULL,
  store_id INTEGER NOT NULL REFERENCES stores(id) ON DELETE RESTRICT,
  trip_date DATE DEFAULT CURRENT_DATE,
  planned_cost DECIMAL(10,2),
  actual_cost DECIMAL(10,2),
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_shopping_trips_plan ON shopping_trips(meal_plan_id);
CREATE INDEX idx_shopping_trips_date ON shopping_trips(trip_date);
```

**Fields**:
- `id`: Auto-incrementing primary key
- `meal_plan_id`: Optional link to associated meal plan
- `store_id`: Foreign key to stores where shopping occurred
- `trip_date`: Date of shopping trip
- `planned_cost`: Pre-calculated expected cost from shopping list
- `actual_cost`: Receipt total after shopping
- `notes`: Variance explanations, items out of stock, substitutions

**Sample Data**:
```sql
INSERT INTO shopping_trips (meal_plan_id, store_id, planned_cost, actual_cost, trip_date) VALUES
  (1, 1, 87.50, 84.23, '2025-02-05');
```

---

## Database Initialization Script
```sql
-- Create all tables in dependency order
-- Run this script to initialize the database

-- 1. Independent tables (no foreign keys)
CREATE TABLE food_items (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL UNIQUE,
  category VARCHAR(100),
  default_unit VARCHAR(50) DEFAULT 'each',
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE recipes (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  description TEXT,
  servings INTEGER DEFAULT 4,
  prep_time INTEGER,
  cook_time INTEGER,
  instructions TEXT,
  cuisine_type VARCHAR(100),
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE stores (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  location VARCHAR(255),
  store_type VARCHAR(100) DEFAULT 'grocery',
  website VARCHAR(255),
  notes TEXT,
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE meal_plans (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) DEFAULT 'Weekly Plan',
  start_date DATE NOT NULL,
  end_date DATE NOT NULL,
  status VARCHAR(50) DEFAULT 'planning',
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. Dependent tables (have foreign keys)
CREATE TABLE recipe_ingredients (
  id SERIAL PRIMARY KEY,
  recipe_id INTEGER NOT NULL REFERENCES recipes(id) ON DELETE CASCADE,
  food_item_id INTEGER NOT NULL REFERENCES food_items(id) ON DELETE RESTRICT,
  quantity DECIMAL(10,2) NOT NULL,
  unit VARCHAR(50) NOT NULL,
  preparation_notes VARCHAR(255),
  optional BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE prices (
  id SERIAL PRIMARY KEY,
  food_item_id INTEGER NOT NULL REFERENCES food_items(id) ON DELETE CASCADE,
  store_id INTEGER NOT NULL REFERENCES stores(id) ON DELETE CASCADE,
  price DECIMAL(10,2) NOT NULL,
  unit VARCHAR(50) NOT NULL,
  package_size VARCHAR(100),
  date_recorded DATE DEFAULT CURRENT_DATE,
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE meal_plan_items (
  id SERIAL PRIMARY KEY,
  meal_plan_id INTEGER NOT NULL REFERENCES meal_plans(id) ON DELETE CASCADE,
  recipe_id INTEGER NOT NULL REFERENCES recipes(id) ON DELETE RESTRICT,
  meal_date DATE NOT NULL,
  meal_type VARCHAR(50) DEFAULT 'dinner',
  servings INTEGER DEFAULT 4,
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE shopping_trips (
  id SERIAL PRIMARY KEY,
  meal_plan_id INTEGER REFERENCES meal_plans(id) ON DELETE SET NULL,
  store_id INTEGER NOT NULL REFERENCES stores(id) ON DELETE RESTRICT,
  trip_date DATE DEFAULT CURRENT_DATE,
  planned_cost DECIMAL(10,2),
  actual_cost DECIMAL(10,2),
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 3. Indexes for performance
CREATE INDEX idx_food_items_category ON food_items(category);
CREATE INDEX idx_food_items_name ON food_items(name);
CREATE INDEX idx_recipes_name ON recipes(name);
CREATE INDEX idx_recipes_cuisine ON recipes(cuisine_type);
CREATE INDEX idx_recipe_ingredients_recipe ON recipe_ingredients(recipe_id);
CREATE INDEX idx_recipe_ingredients_food ON recipe_ingredients(food_item_id);
CREATE INDEX idx_stores_name ON stores(name);
CREATE INDEX idx_prices_food_item ON prices(food_item_id);
CREATE INDEX idx_prices_store ON prices(store_id);
CREATE INDEX idx_prices_date ON prices(date_recorded);
CREATE UNIQUE INDEX idx_prices_latest ON prices(food_item_id, store_id, date_recorded);
CREATE INDEX idx_meal_plans_dates ON meal_plans(start_date, end_date);
CREATE INDEX idx_meal_plan_items_plan ON meal_plan_items(meal_plan_id);
CREATE INDEX idx_meal_plan_items_date ON meal_plan_items(meal_date);
CREATE INDEX idx_shopping_trips_plan ON shopping_trips(meal_plan_id);
CREATE INDEX idx_shopping_trips_date ON shopping_trips(trip_date);
```

---

## Seed Data (Optional Development Data)
```sql
-- Sample categories for food items
-- Meat, Dairy, Produce, Pantry, Frozen, Bakery, Beverages, Snacks

-- Sample stores (Primary shopping locations)
INSERT INTO stores (name, location, store_type) VALUES
  ('Kroger', 'Local Kroger store', 'grocery'),
  ('Walmart', 'Local Walmart Supercenter', 'warehouse'),
  ('Whole Foods', 'Local Whole Foods Market', 'specialty');

-- Sample food items (common staples)
INSERT INTO food_items (name, category, default_unit) VALUES
  ('Chicken Breast Boneless', 'Meat', 'lb'),
  ('Ground Beef 80/20', 'Meat', 'lb'),
  ('Salmon Fillet', 'Meat', 'lb'),
  ('Whole Milk', 'Dairy', 'gallon'),
  ('Eggs Large', 'Dairy', 'dozen'),
  ('Cheddar Cheese', 'Dairy', 'lb'),
  ('Yellow Onion', 'Produce', 'lb'),
  ('Garlic', 'Produce', 'each'),
  ('Bell Pepper', 'Produce', 'each'),
  ('Broccoli', 'Produce', 'lb'),
  ('Olive Oil', 'Pantry', 'oz'),
  ('Soy Sauce', 'Pantry', 'oz'),
  ('Rice Long Grain', 'Pantry', 'lb'),
  ('Pasta Spaghetti', 'Pantry', 'lb'),
  ('Salt', 'Pantry', 'oz');

-- Sample prices (realistic ranges)
-- Store IDs: 1=Kroger, 2=Walmart, 3=Whole Foods
INSERT INTO prices (food_item_id, store_id, price, unit, date_recorded) VALUES
  -- Chicken Breast (food_item_id=1)
  (1, 1, 3.99, 'lb', CURRENT_DATE),   -- Kroger
  (1, 2, 3.49, 'lb', CURRENT_DATE),   -- Walmart
  (1, 3, 6.99, 'lb', CURRENT_DATE),   -- Whole Foods
  -- Ground Beef (food_item_id=2)
  (2, 1, 4.99, 'lb', CURRENT_DATE),   -- Kroger
  (2, 2, 4.29, 'lb', CURRENT_DATE),   -- Walmart
  (2, 3, 7.99, 'lb', CURRENT_DATE),   -- Whole Foods
  -- Whole Milk (food_item_id=4)
  (4, 1, 3.29, 'gallon', CURRENT_DATE),  -- Kroger
  (4, 2, 2.98, 'gallon', CURRENT_DATE),  -- Walmart
  (4, 3, 5.99, 'gallon', CURRENT_DATE),  -- Whole Foods
  -- Eggs Large (food_item_id=5)
  (5, 1, 2.49, 'dozen', CURRENT_DATE),   -- Kroger
  (5, 2, 1.98, 'dozen', CURRENT_DATE),   -- Walmart
  (5, 3, 4.99, 'dozen', CURRENT_DATE);   -- Whole Foods

-- Sample recipe
INSERT INTO recipes (name, servings, prep_time, cook_time, instructions) VALUES
  ('Chicken Stir Fry', 4, 15, 20, 
   '1. Cut chicken into 1-inch cubes
2. Heat 2 tbsp oil in wok over high heat
3. Cook chicken until golden, 5-7 minutes
4. Add vegetables and stir fry 5 minutes
5. Add sauce and cook 2 minutes
6. Serve over rice');

-- Recipe ingredients for Chicken Stir Fry
INSERT INTO recipe_ingredients (recipe_id, food_item_id, quantity, unit, preparation_notes) VALUES
  (1, 1, 1.5, 'lb', 'cut into cubes'),
  (1, 7, 1, 'each', 'sliced'),
  (1, 9, 2, 'each', 'sliced'),
  (1, 10, 0.5, 'lb', 'cut into florets'),
  (1, 11, 2, 'tbsp', ''),
  (1, 12, 3, 'tbsp', '');
```

---

## Key Design Decisions

### 1. Price History Strategy
- **Keep all price records** (no updates, only inserts)
- Latest price = most recent `date_recorded` for food_item + store combo
- Enables trend analysis: "Chicken was $3.99 last month, $4.49 now"
- Query pattern: `WHERE date_recorded = (SELECT MAX(date_recorded) FROM prices WHERE ...)`

### 2. Unit Normalization
- **Store as-entered** in recipe_ingredients and prices
- Handle conversions in application layer, not database
- Accept that some conversions are imperfect (e.g., "2 cloves garlic")
- Future enhancement: Standardize to base units (oz, fl oz)

### 3. Soft Deletes vs. Hard Deletes
- **Hard delete**: Recipe ingredients (cascade when recipe deleted)
- **Soft delete**: Stores (is_active flag)
- **Restrict**: Food items with price history (prevent accidental data loss)

### 4. Meal Plan Lifecycle
- **Status values**: planning, active, completed, archived
- Allows reuse of meal plans ("Copy last week's plan")
- Historical meal plans inform future suggestions

### 5. Cost Calculation Approach
- **Not stored** in database (calculated on-demand)
- Recipe cost = SUM(ingredient_quantity * latest_price_per_unit)
- Meal plan cost = SUM(all recipe costs)
- Shopping list cost per store = calculated from current prices

---

## Future Schema Enhancements (Post-MVP)

### Phase 2 Additions
```sql
-- User accounts and household sharing
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Pantry inventory tracking
CREATE TABLE pantry_items (
  id SERIAL PRIMARY KEY,
  food_item_id INTEGER REFERENCES food_items(id),
  quantity DECIMAL(10,2),
  unit VARCHAR(50),
  location VARCHAR(100), -- fridge, freezer, pantry
  expiration_date DATE,
  date_added DATE DEFAULT CURRENT_DATE
);

-- Recipe tags for filtering
CREATE TABLE recipe_tags (
  recipe_id INTEGER REFERENCES recipes(id),
  tag VARCHAR(100),
  PRIMARY KEY (recipe_id, tag)
);
```

These are explicitly **NOT** in MVP scope but documented for future reference.