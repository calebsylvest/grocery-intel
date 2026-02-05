# API Specification

## Overview
RESTful API for Grocery Intelligence application. All endpoints return JSON. Single-user MVP (no authentication).

## Base URL
```
Development: http://localhost:3000/api
Production: https://[TBD]/api
```

## Response Format

### Success Response
```json
{
  "success": true,
  "data": { /* response payload */ }
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message"
  }
}
```

## Common HTTP Status Codes
- `200 OK`: Successful GET/PUT/DELETE
- `201 Created`: Successful POST
- `400 Bad Request`: Invalid input
- `404 Not Found`: Resource doesn't exist
- `500 Internal Server Error`: Server-side error

---

## Endpoints

### Food Items

#### GET /food-items
Get all food items with optional filtering.

**Query Parameters**:
- `category` (optional): Filter by category
- `search` (optional): Search by name (case-insensitive partial match)

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Chicken Breast Boneless",
      "category": "Meat",
      "default_unit": "lb",
      "notes": "Organic preferred",
      "created_at": "2025-02-04T10:00:00Z",
      "updated_at": "2025-02-04T10:00:00Z"
    }
  ]
}
```

#### GET /food-items/:id
Get single food item by ID.

**Response**:
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Chicken Breast Boneless",
    "category": "Meat",
    "default_unit": "lb",
    "notes": "Organic preferred",
    "created_at": "2025-02-04T10:00:00Z",
    "updated_at": "2025-02-04T10:00:00Z"
  }
}
```

#### POST /food-items
Create new food item.

**Request Body**:
```json
{
  "name": "Chicken Breast Boneless",
  "category": "Meat",
  "default_unit": "lb",
  "notes": "Organic preferred"
}
```

**Response**: (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Chicken Breast Boneless",
    "category": "Meat",
    "default_unit": "lb",
    "notes": "Organic preferred",
    "created_at": "2025-02-04T10:00:00Z",
    "updated_at": "2025-02-04T10:00:00Z"
  }
}
```

#### PUT /food-items/:id
Update existing food item.

**Request Body** (all fields optional):
```json
{
  "name": "Chicken Breast Organic",
  "category": "Meat",
  "default_unit": "lb",
  "notes": "Always buy organic"
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Chicken Breast Organic",
    "category": "Meat",
    "default_unit": "lb",
    "notes": "Always buy organic",
    "created_at": "2025-02-04T10:00:00Z",
    "updated_at": "2025-02-04T12:00:00Z"
  }
}
```

#### DELETE /food-items/:id
Delete food item (will fail if referenced by recipes or prices).

**Response**:
```json
{
  "success": true,
  "data": {
    "message": "Food item deleted successfully"
  }
}
```

---

### Recipes

#### GET /recipes
Get all recipes with optional filtering.

**Query Parameters**:
- `cuisine_type` (optional): Filter by cuisine
- `search` (optional): Search by name
- `include_ingredients` (optional): boolean, if true includes full ingredient list

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Chicken Stir Fry",
      "description": "Quick and healthy weeknight dinner",
      "servings": 4,
      "prep_time": 15,
      "cook_time": 20,
      "instructions": "1. Cut chicken...",
      "cuisine_type": "Asian",
      "notes": "Use day-old rice for best results",
      "created_at": "2025-02-04T10:00:00Z",
      "updated_at": "2025-02-04T10:00:00Z",
      "ingredients": [ /* if include_ingredients=true */
        {
          "id": 1,
          "food_item_id": 1,
          "food_item_name": "Chicken Breast Boneless",
          "quantity": 1.5,
          "unit": "lb",
          "preparation_notes": "cut into cubes",
          "optional": false
        }
      ]
    }
  ]
}
```

#### GET /recipes/:id
Get single recipe with full ingredient list.

**Response**:
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Chicken Stir Fry",
    "description": "Quick and healthy weeknight dinner",
    "servings": 4,
    "prep_time": 15,
    "cook_time": 20,
    "instructions": "1. Cut chicken into cubes\n2. Heat oil...",
    "cuisine_type": "Asian",
    "notes": "Use day-old rice",
    "created_at": "2025-02-04T10:00:00Z",
    "updated_at": "2025-02-04T10:00:00Z",
    "ingredients": [
      {
        "id": 1,
        "food_item_id": 1,
        "food_item_name": "Chicken Breast Boneless",
        "quantity": 1.5,
        "unit": "lb",
        "preparation_notes": "cut into cubes",
        "optional": false
      },
      {
        "id": 2,
        "food_item_id": 7,
        "food_item_name": "Yellow Onion",
        "quantity": 1,
        "unit": "each",
        "preparation_notes": "sliced",
        "optional": false
      }
    ]
  }
}
```

#### POST /recipes
Create new recipe with ingredients.

**Request Body**:
```json
{
  "name": "Chicken Stir Fry",
  "description": "Quick and healthy weeknight dinner",
  "servings": 4,
  "prep_time": 15,
  "cook_time": 20,
  "instructions": "1. Cut chicken into cubes\n2. Heat oil in wok...",
  "cuisine_type": "Asian",
  "notes": "Use day-old rice for best results",
  "ingredients": [
    {
      "food_item_id": 1,
      "quantity": 1.5,
      "unit": "lb",
      "preparation_notes": "cut into cubes",
      "optional": false
    },
    {
      "food_item_id": 7,
      "quantity": 1,
      "unit": "each",
      "preparation_notes": "sliced",
      "optional": false
    }
  ]
}
```

**Response**: (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Chicken Stir Fry",
    /* full recipe object with ingredients */
  }
}
```

#### PUT /recipes/:id
Update recipe and optionally replace ingredients.

**Request Body** (all fields optional except where noted):
```json
{
  "name": "Chicken Stir Fry Deluxe",
  "description": "Updated description",
  "servings": 6,
  "prep_time": 20,
  "cook_time": 25,
  "instructions": "Updated instructions...",
  "cuisine_type": "Asian",
  "notes": "New notes",
  "ingredients": [ /* if provided, REPLACES all ingredients */
    {
      "food_item_id": 1,
      "quantity": 2,
      "unit": "lb",
      "preparation_notes": "cubed",
      "optional": false
    }
  ]
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "id": 1,
    /* full updated recipe object */
  }
}
```

#### DELETE /recipes/:id
Delete recipe (will fail if referenced in active meal plans).

**Response**:
```json
{
  "success": true,
  "data": {
    "message": "Recipe deleted successfully"
  }
}
```

---

### Stores

#### GET /stores
Get all stores (active only by default).

**Query Parameters**:
- `include_inactive` (optional): boolean, include soft-deleted stores

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Kroger - Main St",
      "location": "123 Main St, Dallas TX",
      "store_type": "grocery",
      "website": "https://kroger.com",
      "notes": "Open 7am-10pm",
      "is_active": true,
      "created_at": "2025-02-04T10:00:00Z",
      "updated_at": "2025-02-04T10:00:00Z"
    }
  ]
}
```

#### GET /stores/:id
Get single store by ID.

#### POST /stores
Create new store.

**Request Body**:
```json
{
  "name": "Kroger - Main St",
  "location": "123 Main St, Dallas TX",
  "store_type": "grocery",
  "website": "https://kroger.com",
  "notes": "Open 7am-10pm"
}
```

#### PUT /stores/:id
Update store.

#### DELETE /stores/:id
Soft delete store (sets is_active = false).

---

### Prices

#### GET /prices/latest
Get latest prices for all food items across all stores.

**Query Parameters**:
- `food_item_id` (optional): Filter to specific food item
- `store_id` (optional): Filter to specific store

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "food_item_id": 1,
      "food_item_name": "Chicken Breast Boneless",
      "store_id": 1,
      "store_name": "Kroger - Main St",
      "price": 3.99,
      "unit": "lb",
      "package_size": "loose",
      "date_recorded": "2025-02-04",
      "notes": null
    },
    {
      "id": 2,
      "food_item_id": 1,
      "food_item_name": "Chicken Breast Boneless",
      "store_id": 2,
      "store_name": "Walmart Supercenter",
      "price": 4.49,
      "unit": "lb",
      "package_size": "loose",
      "date_recorded": "2025-02-04",
      "notes": null
    }
  ]
}
```

#### GET /prices/history/:food_item_id
Get price history for specific food item across all stores.

**Query Parameters**:
- `days` (optional): Number of days to look back (default 30)

**Response**:
```json
{
  "success": true,
  "data": {
    "food_item_id": 1,
    "food_item_name": "Chicken Breast Boneless",
    "prices_by_store": [
      {
        "store_id": 1,
        "store_name": "Kroger - Main St",
        "price_history": [
          {
            "price": 3.99,
            "date_recorded": "2025-02-04",
            "unit": "lb"
          },
          {
            "price": 4.29,
            "date_recorded": "2025-01-28",
            "unit": "lb"
          }
        ]
      }
    ]
  }
}
```

#### POST /prices
Record new price observation.

**Request Body**:
```json
{
  "food_item_id": 1,
  "store_id": 1,
  "price": 3.99,
  "unit": "lb",
  "package_size": "loose",
  "date_recorded": "2025-02-04",
  "notes": "On sale this week"
}
```

**Response**: (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "food_item_id": 1,
    "store_id": 1,
    "price": 3.99,
    "unit": "lb",
    "package_size": "loose",
    "date_recorded": "2025-02-04",
    "notes": "On sale this week",
    "created_at": "2025-02-04T10:00:00Z"
  }
}
```

#### PUT /prices/:id
Update price record (rare use case, typically create new record instead).

#### DELETE /prices/:id
Delete price record.

---

### Meal Plans

#### GET /meal-plans
Get all meal plans.

**Query Parameters**:
- `status` (optional): Filter by status (planning, active, completed, archived)
- `start_date_from` (optional): Filter plans starting on or after date (YYYY-MM-DD)
- `start_date_to` (optional): Filter plans starting on or before date

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Week of Feb 5",
      "start_date": "2025-02-05",
      "end_date": "2025-02-11",
      "status": "planning",
      "notes": "Trying new recipes this week",
      "created_at": "2025-02-04T10:00:00Z",
      "updated_at": "2025-02-04T10:00:00Z",
      "meal_count": 7,
      "estimated_cost": 87.50
    }
  ]
}
```

#### GET /meal-plans/:id
Get single meal plan with all scheduled meals.

**Response**:
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Week of Feb 5",
    "start_date": "2025-02-05",
    "end_date": "2025-02-11",
    "status": "planning",
    "notes": "Trying new recipes this week",
    "created_at": "2025-02-04T10:00:00Z",
    "updated_at": "2025-02-04T10:00:00Z",
    "meals": [
      {
        "id": 1,
        "recipe_id": 1,
        "recipe_name": "Chicken Stir Fry",
        "meal_date": "2025-02-05",
        "meal_type": "dinner",
        "servings": 4,
        "notes": null,
        "estimated_cost": 12.50
      },
      {
        "id": 2,
        "recipe_id": 2,
        "recipe_name": "Spaghetti Bolognese",
        "meal_date": "2025-02-06",
        "meal_type": "dinner",
        "servings": 6,
        "notes": "Make extra for leftovers",
        "estimated_cost": 15.75
      }
    ],
    "total_estimated_cost": 87.50
  }
}
```

#### POST /meal-plans
Create new meal plan.

**Request Body**:
```json
{
  "name": "Week of Feb 5",
  "start_date": "2025-02-05",
  "end_date": "2025-02-11",
  "status": "planning",
  "notes": "Trying new recipes this week"
}
```

**Response**: (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Week of Feb 5",
    "start_date": "2025-02-05",
    "end_date": "2025-02-11",
    "status": "planning",
    "notes": "Trying new recipes this week",
    "created_at": "2025-02-04T10:00:00Z",
    "updated_at": "2025-02-04T10:00:00Z"
  }
}
```

#### PUT /meal-plans/:id
Update meal plan metadata (not individual meals).

**Request Body** (all fields optional):
```json
{
  "name": "Updated Week Plan",
  "start_date": "2025-02-05",
  "end_date": "2025-02-11",
  "status": "active",
  "notes": "Updated notes"
}
```

#### DELETE /meal-plans/:id
Delete meal plan and all associated meals (cascade).

---

### Meal Plan Items

#### POST /meal-plans/:meal_plan_id/meals
Add meal to meal plan.

**Request Body**:
```json
{
  "recipe_id": 1,
  "meal_date": "2025-02-05",
  "meal_type": "dinner",
  "servings": 4,
  "notes": "Leftovers for tomorrow's lunch"
}
```

**Response**: (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "meal_plan_id": 1,
    "recipe_id": 1,
    "recipe_name": "Chicken Stir Fry",
    "meal_date": "2025-02-05",
    "meal_type": "dinner",
    "servings": 4,
    "notes": "Leftovers for tomorrow's lunch",
    "estimated_cost": 12.50
  }
}
```

#### PUT /meal-plan-items/:id
Update existing meal in plan.

**Request Body** (all fields optional):
```json
{
  "recipe_id": 2,
  "meal_date": "2025-02-06",
  "meal_type": "dinner",
  "servings": 6,
  "notes": "Changed plans"
}
```

#### DELETE /meal-plan-items/:id
Remove meal from plan.

---

### Shopping Lists

#### GET /meal-plans/:meal_plan_id/shopping-list
Generate aggregated shopping list from meal plan.

**Query Parameters**:
- `store_id` (optional): Calculate costs for specific store

**Response**:
```json
{
  "success": true,
  "data": {
    "meal_plan_id": 1,
    "meal_plan_name": "Week of Feb 5",
    "generated_at": "2025-02-04T10:00:00Z",
    "items": [
      {
        "food_item_id": 1,
        "food_item_name": "Chicken Breast Boneless",
        "category": "Meat",
        "total_quantity": 3.0,
        "unit": "lb",
        "recipes_used_in": ["Chicken Stir Fry", "Chicken Tacos"],
        "prices_by_store": [
          {
            "store_id": 1,
            "store_name": "Kroger - Main St",
            "price_per_unit": 3.99,
            "total_cost": 11.97,
            "unit": "lb"
          },
          {
            "store_id": 2,
            "store_name": "Walmart Supercenter",
            "price_per_unit": 4.49,
            "total_cost": 13.47,
            "unit": "lb"
          }
        ]
      }
    ],
    "cost_summary": [
      {
        "store_id": 1,
        "store_name": "Kroger - Main St",
        "total_cost": 87.43
      },
      {
        "store_id": 2,
        "store_name": "Walmart Supercenter",
        "total_cost": 94.12
      },
      {
        "store_id": 3,
        "store_name": "Whole Foods - Uptown",
        "total_cost": 112.87
      }
    ],
    "best_store": {
      "store_id": 1,
      "store_name": "Kroger - Main St",
      "total_cost": 87.43,
      "savings_vs_most_expensive": 25.44
    }
  }
}
```

---

### Shopping Trips

#### GET /shopping-trips
Get all shopping trips.

**Query Parameters**:
- `meal_plan_id` (optional): Filter to specific meal plan
- `date_from` (optional): Filter trips on or after date
- `date_to` (optional): Filter trips on or before date

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "meal_plan_id": 1,
      "meal_plan_name": "Week of Feb 5",
      "store_id": 1,
      "store_name": "Kroger - Main St",
      "trip_date": "2025-02-05",
      "planned_cost": 87.50,
      "actual_cost": 84.23,
      "variance": -3.27,
      "variance_percent": -3.74,
      "notes": "Onions were on sale",
      "created_at": "2025-02-05T15:00:00Z"
    }
  ]
}
```

#### POST /shopping-trips
Record completed shopping trip.

**Request Body**:
```json
{
  "meal_plan_id": 1,
  "store_id": 1,
  "trip_date": "2025-02-05",
  "planned_cost": 87.50,
  "actual_cost": 84.23,
  "notes": "Onions were on sale, chicken unavailable so bought thighs instead"
}
```

**Response**: (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "meal_plan_id": 1,
    "store_id": 1,
    "trip_date": "2025-02-05",
    "planned_cost": 87.50,
    "actual_cost": 84.23,
    "variance": -3.27,
    "variance_percent": -3.74,
    "notes": "Onions were on sale",
    "created_at": "2025-02-05T15:00:00Z"
  }
}
```

---

### Analytics & Reporting

#### GET /analytics/spending-summary
Get spending summary over time period.

**Query Parameters**:
- `date_from` (required): Start date (YYYY-MM-DD)
- `date_to` (required): End date (YYYY-MM-DD)

**Response**:
```json
{
  "success": true,
  "data": {
    "period": {
      "start": "2025-01-01",
      "end": "2025-01-31"
    },
    "total_planned": 350.00,
    "total_actual": 332.15,
    "total_saved": 17.85,
    "savings_percent": 5.10,
    "trip_count": 4,
    "average_trip_cost": 83.04,
    "by_store": [
      {
        "store_id": 1,
        "store_name": "Kroger - Main St",
        "trip_count": 3,
        "total_spent": 249.12
      },
      {
        "store_id": 2,
        "store_name": "Walmart Supercenter",
        "trip_count": 1,
        "total_spent": 83.03
      }
    ],
    "weekly_breakdown": [
      {
        "week_start": "2025-01-01",
        "planned": 87.50,
        "actual": 84.23
      }
    ]
  }
}
```

#### GET /analytics/recipe-costs
Get cost analysis for all recipes.

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "recipe_id": 1,
      "recipe_name": "Chicken Stir Fry",
      "servings": 4,
      "total_cost": 12.50,
      "cost_per_serving": 3.13,
      "times_cooked": 8,
      "last_cooked": "2025-02-01"
    }
  ]
}
```

---

## Error Codes

| Code | Message | Description |
|------|---------|-------------|
| VALIDATION_ERROR | Invalid input data | Request body fails validation |
| NOT_FOUND | Resource not found | Requested ID doesn't exist |
| DUPLICATE_ENTRY | Duplicate entry | Unique constraint violation |
| FOREIGN_KEY_VIOLATION | Referenced resource not found | FK constraint violation |
| DELETE_RESTRICTED | Cannot delete resource with dependencies | Cascade prevent |
| DATABASE_ERROR | Database operation failed | General DB error |
| INTERNAL_ERROR | Internal server error | Unexpected server error |

---

## Implementation Notes

### Transaction Handling
- Recipe create/update with ingredients: Single transaction
- Meal plan deletion: Cascade in single transaction
- Shopping trip recording: Atomic operation

### Caching Strategy (Future)
- Latest prices: Cache for 1 hour
- Recipe list: Cache for 5 minutes
- Shopping list: Calculate fresh (no cache)

### Rate Limiting (Future)
- Not implemented in MVP (single user)
- Future: 100 requests/minute per IP

### CORS
- Development: Allow localhost:3001 (React dev server)
- Production: Allow only production frontend domain