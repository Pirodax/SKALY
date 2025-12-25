# SKALY - Data Model & Database Schema

## Database: Supabase (PostgreSQL)

### Schema Overview

```
users (Auth - Supabase Auth)
  ├─→ profiles
  ├─→ businesses
  │    ├─→ transactions
  │    └─→ business_metrics (view)
  ├─→ personal_transactions
  ├─→ subscriptions
  ├─→ budgets
  ├─→ assets
  ├─→ liabilities
  ├─→ investment_goals
  └─→ investment_allocations
```

---

## Tables

### 1. profiles

**Purpose**: Extended user profile data (1-to-1 with Supabase auth.users)

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, FK(auth.users) | User ID from Supabase Auth |
| email | text | NOT NULL | User email (mirrored from auth) |
| first_name | text | | First name |
| status | text | NOT NULL | 'salaried', 'freelance', 'entrepreneur', 'mixed' |
| primary_goal | text | | 'real_estate', 'emergency_fund', 'retirement', etc. |
| tax_rate | numeric(5,2) | DEFAULT 30.00 | Estimated income tax rate (%) |
| security_months | numeric(3,1) | DEFAULT 3.0 | Security provision (months of expenses) |
| created_at | timestamptz | DEFAULT now() | Account creation date |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- UNIQUE (email)

**RLS Policies:**
```sql
-- Users can only read/update their own profile
CREATE POLICY "Users can view own profile" ON profiles
  FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles
  FOR UPDATE USING (auth.uid() = id);
```

---

### 2. businesses

**Purpose**: User's professional activities/businesses

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Business ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| name | text | NOT NULL | Business name |
| type | text | NOT NULL | 'freelance', 'ecommerce', 'saas', 'rental', 'other' |
| legal_status | text | NOT NULL | 'micro_entreprise', 'sasu', 'eurl', 'sas', 'sarl', 'liberal' |
| urssaf_rate | numeric(5,2) | NOT NULL | URSSAF contribution rate (%) |
| cfe_annual | numeric(10,2) | DEFAULT 0 | Annual CFE amount |
| created_date | date | NOT NULL | Business creation date |
| is_active | boolean | DEFAULT true | Active status |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (user_id)
- UNIQUE (user_id, name) WHERE is_active = true

**RLS Policies:**
```sql
CREATE POLICY "Users can view own businesses" ON businesses
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can insert own businesses" ON businesses
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own businesses" ON businesses
  FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own businesses" ON businesses
  FOR DELETE USING (auth.uid() = user_id);
```

---

### 3. transactions

**Purpose**: Business-related transactions (revenues & expenses)

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Transaction ID |
| business_id | uuid | FK(businesses), NOT NULL | Related business |
| user_id | uuid | FK(profiles), NOT NULL | Owner (denormalized for RLS) |
| type | text | NOT NULL | 'revenue' or 'expense' |
| amount | numeric(10,2) | NOT NULL, CHECK > 0 | Amount in euros |
| category | text | NOT NULL | Category (client, tools, ads, etc.) |
| description | text | | Transaction description |
| date | date | NOT NULL | Transaction date |
| is_recurring | boolean | DEFAULT false | Recurring transaction flag |
| recurrence_frequency | text | | 'monthly', 'quarterly', 'yearly' |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (business_id, date DESC)
- INDEX (user_id, date DESC)
- INDEX (type)

**RLS Policies:**
```sql
CREATE POLICY "Users can view own business transactions" ON transactions
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can insert own business transactions" ON transactions
  FOR INSERT WITH CHECK (
    auth.uid() = user_id AND
    EXISTS (SELECT 1 FROM businesses WHERE id = business_id AND user_id = auth.uid())
  );

CREATE POLICY "Users can update own business transactions" ON transactions
  FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own business transactions" ON transactions
  FOR DELETE USING (auth.uid() = user_id);
```

---

### 4. personal_transactions

**Purpose**: Personal (non-business) transactions

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Transaction ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| type | text | NOT NULL | 'income' or 'expense' |
| amount | numeric(10,2) | NOT NULL, CHECK > 0 | Amount in euros |
| category | text | NOT NULL | 'salary', 'rent', 'food', 'leisure', etc. |
| description | text | | Transaction description |
| date | date | NOT NULL | Transaction date |
| is_recurring | boolean | DEFAULT false | Recurring transaction flag |
| recurrence_frequency | text | | 'monthly', 'quarterly', 'yearly' |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (user_id, date DESC)
- INDEX (type, category)

**RLS Policies:** (same pattern as business transactions)

---

### 5. subscriptions

**Purpose**: Recurring subscriptions tracking

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Subscription ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| context | text | NOT NULL | 'personal' or 'business' |
| business_id | uuid | FK(businesses), NULL | If business subscription |
| name | text | NOT NULL | Service name (Netflix, etc.) |
| amount | numeric(10,2) | NOT NULL, CHECK > 0 | Monthly amount |
| frequency | text | NOT NULL | 'monthly', 'yearly' |
| next_payment_date | date | NOT NULL | Next billing date |
| category | text | NOT NULL | Subscription category |
| is_active | boolean | DEFAULT true | Active status |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (user_id, next_payment_date)
- INDEX (is_active)

---

### 6. budgets

**Purpose**: Budget limits by category

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Budget ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| category | text | NOT NULL | Category name |
| monthly_limit | numeric(10,2) | NOT NULL, CHECK > 0 | Monthly budget limit |
| alert_threshold | numeric(3,0) | DEFAULT 80 | Alert at X% (80 = 80%) |
| is_active | boolean | DEFAULT true | Active status |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- UNIQUE (user_id, category) WHERE is_active = true

---

### 7. assets

**Purpose**: User's assets (savings, investments, real estate, etc.)

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Asset ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| type | text | NOT NULL | 'liquid_savings', 'investment', 'real_estate', 'vehicle', 'other' |
| subtype | text | | 'livret_a', 'pea', 'crypto', 'primary_residence', etc. |
| name | text | NOT NULL | Asset name/description |
| current_value | numeric(12,2) | NOT NULL | Current value in euros |
| interest_rate | numeric(5,2) | DEFAULT 0 | Annual interest/return rate (%) |
| purchase_date | date | | Purchase/acquisition date |
| purchase_value | numeric(12,2) | | Original purchase value |
| is_active | boolean | DEFAULT true | Active status |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (user_id, type)

---

### 8. liabilities

**Purpose**: User's debts and liabilities

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Liability ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| type | text | NOT NULL | 'mortgage', 'loan', 'credit', 'debt' |
| name | text | NOT NULL | Liability name/description |
| initial_amount | numeric(12,2) | NOT NULL | Original amount borrowed |
| remaining_amount | numeric(12,2) | NOT NULL | Remaining balance |
| monthly_payment | numeric(10,2) | NOT NULL | Monthly payment |
| interest_rate | numeric(5,2) | DEFAULT 0 | Annual interest rate (%) |
| start_date | date | NOT NULL | Loan start date |
| end_date | date | | Expected payoff date |
| is_active | boolean | DEFAULT true | Active status |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (user_id)

---

### 9. investment_goals

**Purpose**: User's savings/investment goals with targets

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Goal ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| name | text | NOT NULL | Goal name (e.g., "Apport immobilier") |
| target_amount | numeric(12,2) | NOT NULL, CHECK > 0 | Target amount |
| current_amount | numeric(12,2) | DEFAULT 0 | Current progress |
| target_date | date | | Target completion date |
| priority | integer | DEFAULT 1 | Priority (1 = highest) |
| is_active | boolean | DEFAULT true | Active status |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (user_id, priority)

---

### 10. investment_allocations

**Purpose**: User's desired investment allocation strategy

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Allocation ID |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| category | text | NOT NULL | 'emergency_fund', 'pea', 'life_insurance', 'crypto', 'other' |
| target_percentage | numeric(5,2) | NOT NULL, CHECK 0-100 | Target allocation (%) |
| is_active | boolean | DEFAULT true | Active status |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- UNIQUE (user_id, category) WHERE is_active = true

**Constraint:**
```sql
-- Ensure total allocations = 100%
CREATE FUNCTION check_allocation_total() RETURNS TRIGGER AS $$
BEGIN
  IF (SELECT SUM(target_percentage) FROM investment_allocations
      WHERE user_id = NEW.user_id AND is_active = true) > 100 THEN
    RAISE EXCEPTION 'Total allocation cannot exceed 100%%';
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER validate_allocation_total
  AFTER INSERT OR UPDATE ON investment_allocations
  FOR EACH ROW EXECUTE FUNCTION check_allocation_total();
```

---

## Views

### business_metrics (Materialized View)

**Purpose**: Pre-calculated business metrics for performance

```sql
CREATE MATERIALIZED VIEW business_metrics AS
SELECT
  b.id AS business_id,
  b.user_id,
  b.name,
  DATE_TRUNC('month', t.date) AS month,
  SUM(CASE WHEN t.type = 'revenue' THEN t.amount ELSE 0 END) AS total_revenue,
  SUM(CASE WHEN t.type = 'expense' THEN t.amount ELSE 0 END) AS total_expenses,
  SUM(CASE WHEN t.type = 'revenue' THEN t.amount ELSE -t.amount END) AS gross_profit,
  COUNT(*) AS transaction_count
FROM businesses b
LEFT JOIN transactions t ON t.business_id = b.id
WHERE b.is_active = true
GROUP BY b.id, b.user_id, b.name, DATE_TRUNC('month', t.date);

CREATE UNIQUE INDEX ON business_metrics (business_id, month);
CREATE INDEX ON business_metrics (user_id, month DESC);

-- Refresh strategy: hourly or on-demand
CREATE OR REPLACE FUNCTION refresh_business_metrics()
RETURNS void AS $$
BEGIN
  REFRESH MATERIALIZED VIEW CONCURRENTLY business_metrics;
END;
$$ LANGUAGE plpgsql;
```

---

### user_monthly_summary (View)

**Purpose**: Monthly financial summary per user

```sql
CREATE VIEW user_monthly_summary AS
SELECT
  p.id AS user_id,
  DATE_TRUNC('month', CURRENT_DATE) AS month,

  -- Personal income
  (SELECT COALESCE(SUM(amount), 0)
   FROM personal_transactions
   WHERE user_id = p.id
     AND type = 'income'
     AND date >= DATE_TRUNC('month', CURRENT_DATE)
  ) AS personal_income,

  -- Personal expenses
  (SELECT COALESCE(SUM(amount), 0)
   FROM personal_transactions
   WHERE user_id = p.id
     AND type = 'expense'
     AND date >= DATE_TRUNC('month', CURRENT_DATE)
  ) AS personal_expenses,

  -- Business revenue
  (SELECT COALESCE(SUM(amount), 0)
   FROM transactions t
   JOIN businesses b ON t.business_id = b.id
   WHERE b.user_id = p.id
     AND t.type = 'revenue'
     AND t.date >= DATE_TRUNC('month', CURRENT_DATE)
  ) AS business_revenue,

  -- Business expenses
  (SELECT COALESCE(SUM(amount), 0)
   FROM transactions t
   JOIN businesses b ON t.business_id = b.id
   WHERE b.user_id = p.id
     AND t.type = 'expense'
     AND t.date >= DATE_TRUNC('month', CURRENT_DATE)
  ) AS business_expenses

FROM profiles p;
```

---

## Migrations Strategy

### Migration Files Location
```
supabase/migrations/
  ├── 20251225000001_initial_schema.sql
  ├── 20251225000002_rls_policies.sql
  ├── 20251225000003_views_and_functions.sql
  └── ...
```

### Migration Workflow
```bash
# Create new migration
npx supabase migration new feature_name

# Apply migrations locally
npx supabase db reset

# Apply to production
npx supabase db push
```

---

## Data Relationships Diagram

```
┌─────────────┐
│   profiles  │
│  (users)    │
└──────┬──────┘
       │
       ├─────────────┬─────────────┬──────────────┬─────────────┐
       │             │             │              │             │
       ▼             ▼             ▼              ▼             ▼
┌───────────┐ ┌──────────┐ ┌──────────┐ ┌────────────┐ ┌──────────────┐
│ businesses│ │ personal_│ │  budgets │ │   assets   │ │ liabilities  │
│           │ │transactio│ │          │ │            │ │              │
└─────┬─────┘ │    ns    │ └──────────┘ └────────────┘ └──────────────┘
      │       └──────────┘
      │
      ├─────────────┬─────────────┐
      ▼             ▼             ▼
┌───────────┐ ┌──────────┐ ┌─────────────┐
│transactio│ │subscripti│ │ investment_ │
│    ns    │ │   ons    │ │   goals     │
└───────────┘ └──────────┘ └─────────────┘
```

---

## Sample Data

### Example User Setup
```sql
-- User profile
INSERT INTO profiles (id, email, first_name, status, primary_goal) VALUES
('user-uuid', 'marie@example.com', 'Marie', 'freelance', 'real_estate');

-- Business
INSERT INTO businesses (user_id, name, type, legal_status, urssaf_rate) VALUES
('user-uuid', 'Freelance Design', 'freelance', 'micro_entreprise', 22.00);

-- Transactions
INSERT INTO transactions (business_id, user_id, type, amount, category, description, date) VALUES
('business-uuid', 'user-uuid', 'revenue', 2500.00, 'Client A', 'Logo design', '2025-12-15'),
('business-uuid', 'user-uuid', 'expense', 49.00, 'Tools', 'Figma subscription', '2025-12-01');
```

---

**Last Updated**: 2025-12-25
