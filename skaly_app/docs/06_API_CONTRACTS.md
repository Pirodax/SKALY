# SKALY - API Contracts & Endpoints

## API Architecture

**Framework**: Next.js 16 App Router API Routes
**Base URL**: `/api`
**Authentication**: Supabase JWT (Bearer token)
**Response Format**: JSON
**Error Format**: RFC 7807 Problem Details

---

## Authentication

All API routes (except auth routes) require authentication.

**Headers:**
```http
Authorization: Bearer <supabase_jwt_token>
Content-Type: application/json
```

**Error Responses:**
```json
{
  "error": "Unauthorized",
  "message": "Missing or invalid authentication token",
  "statusCode": 401
}
```

---

## API Routes

### 1. Dashboard

#### GET `/api/dashboard/summary`

**Description**: Get dashboard summary data

**Response:**
```json
{
  "personalBalance": 200.00,
  "businessBalance": 2800.00,
  "investmentCapacity": 850.00,
  "netWorth": 67500.00,
  "trends": {
    "personalBalanceTrend": 15.2,
    "businessBalanceTrend": -5.1,
    "netWorthTrend": 12.5
  }
}
```

---

### 2. Personal Transactions

#### GET `/api/personal/transactions`

**Description**: Get personal transactions with filters

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| month | string | No | YYYY-MM format |
| category | string | No | Filter by category |
| type | string | No | 'income' or 'expense' |
| page | number | No | Page number (default: 1) |
| limit | number | No | Items per page (default: 50) |

**Response:**
```json
{
  "transactions": [
    {
      "id": "uuid",
      "type": "expense",
      "amount": 85.00,
      "category": "food",
      "description": "Courses Carrefour",
      "date": "2025-12-10",
      "isRecurring": false,
      "createdAt": "2025-12-10T14:32:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 50,
    "total": 127,
    "totalPages": 3
  },
  "summary": {
    "totalIncome": 2000.00,
    "totalExpenses": 1800.00,
    "balance": 200.00
  }
}
```

#### POST `/api/personal/transactions`

**Description**: Create a new personal transaction

**Request Body:**
```json
{
  "type": "expense",
  "amount": 85.00,
  "category": "food",
  "description": "Courses Carrefour",
  "date": "2025-12-10",
  "isRecurring": false,
  "recurrenceFrequency": null
}
```

**Validation:**
- `type`: Required, must be 'income' or 'expense'
- `amount`: Required, must be > 0
- `category`: Required, must be valid category
- `date`: Required, cannot be in future
- `description`: Optional, max 500 chars

**Response:**
```json
{
  "id": "uuid",
  "type": "expense",
  "amount": 85.00,
  "category": "food",
  "description": "Courses Carrefour",
  "date": "2025-12-10",
  "isRecurring": false,
  "createdAt": "2025-12-10T14:32:00Z"
}
```

#### PUT `/api/personal/transactions/:id`

**Description**: Update a transaction

**Request Body:** (same as POST, all fields optional)

**Response:** Updated transaction object

#### DELETE `/api/personal/transactions/:id`

**Description**: Delete a transaction

**Response:**
```json
{
  "success": true,
  "message": "Transaction deleted"
}
```

---

### 3. Businesses

#### GET `/api/businesses`

**Description**: Get user's businesses

**Response:**
```json
{
  "businesses": [
    {
      "id": "uuid",
      "name": "Freelance Design",
      "type": "freelance",
      "legalStatus": "micro_entreprise",
      "urssafRate": 21.2,
      "cfeAnnual": 500.00,
      "createdDate": "2022-01-15",
      "isActive": true,
      "metrics": {
        "monthlyRevenue": 4000.00,
        "monthlyExpenses": 800.00,
        "netProfit": 1520.00,
        "profitMargin": 38.0
      }
    }
  ]
}
```

#### POST `/api/businesses`

**Description**: Create a new business

**Request Body:**
```json
{
  "name": "Freelance Design",
  "type": "freelance",
  "legalStatus": "micro_entreprise",
  "urssafRate": 21.2,
  "cfeAnnual": 500.00,
  "createdDate": "2022-01-15"
}
```

**Validation:**
- `name`: Required, unique per user, max 100 chars
- `type`: Required, valid business type
- `legalStatus`: Required, valid legal status
- `urssafRate`: Required, 0-100
- `cfeAnnual`: Optional, >= 0
- `createdDate`: Required, cannot be in future

**Response:** Created business object

#### PUT `/api/businesses/:id`

**Description**: Update a business

**Request Body:** (same as POST, all fields optional)

**Response:** Updated business object

#### DELETE `/api/businesses/:id`

**Description**: Soft delete a business (sets isActive to false)

**Response:**
```json
{
  "success": true,
  "message": "Business archived"
}
```

---

### 4. Business Transactions

#### GET `/api/businesses/:businessId/transactions`

**Description**: Get transactions for a specific business

**Query Parameters:** (same as personal transactions)

**Response:** (same structure as personal transactions)

#### POST `/api/businesses/:businessId/transactions`

**Description**: Create a business transaction

**Request Body:**
```json
{
  "type": "revenue",
  "amount": 2500.00,
  "category": "Client A",
  "description": "Logo design",
  "date": "2025-12-15",
  "isRecurring": false
}
```

**Response:** Created transaction object

---

### 5. Budgets

#### GET `/api/budgets`

**Description**: Get user's budgets

**Response:**
```json
{
  "budgets": [
    {
      "id": "uuid",
      "category": "food",
      "monthlyLimit": 400.00,
      "alertThreshold": 80,
      "isActive": true,
      "currentSpending": 320.00,
      "percentage": 80.0,
      "status": "warning"
    }
  ]
}
```

#### POST `/api/budgets`

**Description**: Create a budget

**Request Body:**
```json
{
  "category": "food",
  "monthlyLimit": 400.00,
  "alertThreshold": 80
}
```

**Response:** Created budget object

---

### 6. Investment

#### GET `/api/investment/capacity`

**Description**: Calculate current investment capacity

**Response:**
```json
{
  "capacity": 850.00,
  "breakdown": {
    "personalIncome": 2000.00,
    "personalExpenses": 1800.00,
    "businessProfit": 1494.00,
    "securityProvision": 5400.00
  },
  "allocation": [
    {
      "category": "emergency_fund",
      "percentage": 20,
      "amount": 170.00
    },
    {
      "category": "pea",
      "percentage": 40,
      "amount": 340.00
    }
  ]
}
```

#### GET `/api/investment/goals`

**Description**: Get investment goals

**Response:**
```json
{
  "goals": [
    {
      "id": "uuid",
      "name": "Apport immobilier",
      "targetAmount": 30000.00,
      "currentAmount": 13500.00,
      "targetDate": "2027-12-31",
      "priority": 1,
      "progress": 45.0,
      "status": "on_track",
      "monthlyNeeded": 550.00
    }
  ]
}
```

#### POST `/api/investment/goals`

**Description**: Create an investment goal

**Request Body:**
```json
{
  "name": "Apport immobilier",
  "targetAmount": 30000.00,
  "currentAmount": 13500.00,
  "targetDate": "2027-12-31",
  "priority": 1
}
```

**Response:** Created goal object

---

### 7. Net Worth

#### GET `/api/net-worth`

**Description**: Get current net worth

**Response:**
```json
{
  "netWorth": 67500.00,
  "assets": {
    "liquidSavings": 20500.00,
    "investments": 37000.00,
    "realEstate": 250000.00,
    "vehicles": 0,
    "other": 0,
    "total": 307500.00
  },
  "liabilities": {
    "mortgages": 180000.00,
    "loans": 8000.00,
    "debts": 0,
    "total": 188000.00
  },
  "trends": {
    "month": 12.5,
    "year": 15.2
  }
}
```

#### GET `/api/net-worth/projection`

**Description**: Project net worth growth

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| years | number | No | Years to project (default: 10) |
| annualReturn | number | No | Expected return % (default: 5) |

**Response:**
```json
{
  "projections": [
    {
      "year": 0,
      "netWorth": 67500.00
    },
    {
      "year": 1,
      "netWorth": 78500.00
    },
    {
      "year": 5,
      "netWorth": 145000.00
    },
    {
      "year": 10,
      "netWorth": 250000.00
    }
  ],
  "assumptions": {
    "monthlyInvestment": 850.00,
    "annualReturn": 5.0
  }
}
```

---

### 8. Assets

#### GET `/api/assets`

**Description**: Get user's assets

**Response:**
```json
{
  "assets": [
    {
      "id": "uuid",
      "type": "liquid_savings",
      "subtype": "livret_a",
      "name": "Livret A",
      "currentValue": 12500.00,
      "interestRate": 3.0,
      "isActive": true
    }
  ],
  "totalValue": 307500.00
}
```

#### POST `/api/assets`

**Description**: Create an asset

**Request Body:**
```json
{
  "type": "liquid_savings",
  "subtype": "livret_a",
  "name": "Livret A",
  "currentValue": 12500.00,
  "interestRate": 3.0,
  "purchaseDate": "2020-01-01",
  "purchaseValue": 10000.00
}
```

**Response:** Created asset object

---

### 9. Subscriptions

#### GET `/api/subscriptions`

**Description**: Get active subscriptions

**Response:**
```json
{
  "subscriptions": [
    {
      "id": "uuid",
      "name": "Netflix",
      "amount": 15.99,
      "frequency": "monthly",
      "nextPaymentDate": "2025-12-28",
      "category": "entertainment",
      "context": "personal",
      "isActive": true
    }
  ],
  "summary": {
    "monthlyTotal": 145.50,
    "yearlyTotal": 1746.00,
    "count": 8
  }
}
```

#### POST `/api/subscriptions`

**Description**: Create a subscription

**Request Body:**
```json
{
  "name": "Netflix",
  "amount": 15.99,
  "frequency": "monthly",
  "nextPaymentDate": "2025-12-28",
  "category": "entertainment",
  "context": "personal"
}
```

**Response:** Created subscription object

---

## Error Handling

### Standard Error Response

```json
{
  "error": "ErrorType",
  "message": "Human-readable error message",
  "statusCode": 400,
  "details": {
    "field": "amount",
    "issue": "Must be greater than 0"
  }
}
```

### Common Error Codes

| Code | Error | Description |
|------|-------|-------------|
| 400 | Bad Request | Invalid request body or parameters |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 422 | Unprocessable Entity | Validation error |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server error |

---

## Rate Limiting

**Limits:**
- Free tier: 100 requests/minute
- Pro tier: 500 requests/minute

**Headers:**
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 85
X-RateLimit-Reset: 1640000000
```

---

## Webhooks (Future)

**Endpoints:**
- `POST /api/webhooks/bank` - Bank transaction updates
- `POST /api/webhooks/payment` - Payment confirmations

---

**Last Updated**: 2025-12-25
