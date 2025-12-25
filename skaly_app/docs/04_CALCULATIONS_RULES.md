# SKALY - Calculation Rules & Business Logic

## Core Calculation Formulas

### 1. Investment Capacity Calculation

**Formula:**
```
Monthly Investment Capacity =
    Recurring Personal Income
  + Net Business Profits (after provisions)
  - Recurring Personal Expenses
  - Security Provision
```

**Detailed Breakdown:**

#### Step 1: Calculate Personal Net Income
```typescript
const personalIncome = sumPersonalTransactions('income', currentMonth)
const personalExpenses = sumPersonalTransactions('expense', currentMonth)
const personalNet = personalIncome - personalExpenses
```

#### Step 2: Calculate Net Business Profits
```typescript
for each business {
  const revenue = sumBusinessTransactions(business, 'revenue', currentMonth)
  const expenses = sumBusinessTransactions(business, 'expense', currentMonth)
  const grossProfit = revenue - expenses

  // Calculate provisions
  const urssafProvision = grossProfit * (business.urssaf_rate / 100)
  const cfeMonthly = business.cfe_annual / 12
  const incomeTaxProvision = grossProfit * (user.tax_rate / 100)

  const netProfit = grossProfit - urssafProvision - cfeMonthly - incomeTaxProvision

  totalBusinessNet += netProfit
}
```

#### Step 3: Calculate Security Provision
```typescript
const averageMonthlyExpenses = calculateAverageExpenses(lastNMonths: 3)
const securityProvision = averageMonthlyExpenses * user.security_months
```

#### Step 4: Final Capacity
```typescript
const investmentCapacity = Math.max(0,
  personalNet +
  totalBusinessNet -
  securityProvision
)
```

**Example:**
```
Personal Income:       2000€
Personal Expenses:    -1800€
Personal Net:          200€

Business Revenue:      4000€
Business Expenses:     -800€
Gross Profit:          3200€

URSSAF (22%):         -704€
Income Tax (30%):     -960€
CFE (monthly):         -42€
Net Business Profit:   1494€

Security (3 months × 1800€): -5400€ (one-time, not monthly)
Wait, security provision should be maintained, not deducted monthly.

Let me recalculate:
Security provision = minimum cash cushion to maintain
If current liquid assets < security provision, deduct shortfall

Actually, simpler approach for MVP:
Security provision is already set aside in assets.
Investment capacity = what's left after all expenses.

Revised formula:
Personal Net:          200€
Net Business Profit:   1494€
Total Available:       1694€

If user wants 3 months security and doesn't have it yet,
they should allocate part of investment capacity to emergency fund first.

Final Investment Capacity: 1694€/month
```

---

### 2. Net Business Profit Calculation

**Purpose**: Calculate actual take-home profit from a business

**Formula:**
```typescript
function calculateNetBusinessProfit(
  business: Business,
  month: Date
): number {
  // 1. Gross profit
  const revenue = sumTransactions(business.id, 'revenue', month)
  const expenses = sumTransactions(business.id, 'expense', month)
  const grossProfit = revenue - expenses

  // 2. URSSAF provisions
  const urssafProvision = grossProfit * (business.urssaf_rate / 100)

  // 3. CFE (annual amount divided by 12)
  const cfeMonthly = business.cfe_annual / 12

  // 4. Income tax provision
  const incomeTaxRate = getUserTaxRate(business.user_id)
  const incomeTaxProvision = grossProfit * (incomeTaxRate / 100)

  // 5. Net profit
  const netProfit = grossProfit - urssafProvision - cfeMonthly - incomeTaxProvision

  return Math.max(0, netProfit) // Never negative
}
```

**URSSAF Rates by Status:**
| Legal Status | Default Rate | Description |
|--------------|--------------|-------------|
| Micro-entreprise (BIC) | 12.3% | Services commerciaux |
| Micro-entreprise (BNC) | 21.2% | Libéral, consulting |
| SASU/EURL | ~45% | Charges patronales + salariales |
| Profession libérale | 22% | CIPAV/URSSAF libéral |

**Example:**
```
Business: Freelance Design (Micro-entreprise BNC)
Revenue: 4000€
Expenses: 800€
Gross Profit: 3200€

URSSAF (21.2%): -678€
Income Tax (30%): -960€
CFE (500€/year): -42€

Net Profit: 1520€
```

---

### 3. Budget Tracking & Alerts

**Purpose**: Monitor spending vs budgets and trigger alerts

**Logic:**
```typescript
function checkBudgetStatus(
  userId: string,
  category: string,
  month: Date
): BudgetStatus {
  // Get budget for category
  const budget = getBudget(userId, category)
  if (!budget) return { status: 'no_budget' }

  // Calculate spending this month
  const spent = sumPersonalTransactions(userId, 'expense', category, month)

  // Calculate percentage
  const percentageUsed = (spent / budget.monthly_limit) * 100

  // Determine status
  if (percentageUsed >= 100) {
    return {
      status: 'exceeded',
      spent,
      budget: budget.monthly_limit,
      percentage: percentageUsed,
      overage: spent - budget.monthly_limit
    }
  } else if (percentageUsed >= budget.alert_threshold) {
    return {
      status: 'warning',
      spent,
      budget: budget.monthly_limit,
      percentage: percentageUsed,
      remaining: budget.monthly_limit - spent
    }
  } else {
    return {
      status: 'ok',
      spent,
      budget: budget.monthly_limit,
      percentage: percentageUsed,
      remaining: budget.monthly_limit - spent
    }
  }
}
```

**Alert Triggers:**
- **Warning**: When spending reaches alert_threshold (default 80%)
- **Exceeded**: When spending > budget limit
- **On Track**: Weekly summary if spending is well-paced

---

### 4. Net Worth Calculation

**Purpose**: Calculate total net worth (assets - liabilities)

**Formula:**
```typescript
function calculateNetWorth(userId: string): NetWorth {
  // 1. Total Assets
  const liquidSavings = sumAssets(userId, 'liquid_savings')
  const investments = sumAssets(userId, 'investment')
  const realEstate = sumAssets(userId, 'real_estate')
  const vehicles = sumAssets(userId, 'vehicle')
  const other = sumAssets(userId, 'other')

  const totalAssets = liquidSavings + investments + realEstate + vehicles + other

  // 2. Total Liabilities
  const mortgages = sumLiabilities(userId, 'mortgage', 'remaining_amount')
  const loans = sumLiabilities(userId, 'loan', 'remaining_amount')
  const debts = sumLiabilities(userId, 'debt', 'remaining_amount')

  const totalLiabilities = mortgages + loans + debts

  // 3. Net Worth
  const netWorth = totalAssets - totalLiabilities

  return {
    assets: {
      liquid: liquidSavings,
      investments,
      realEstate,
      vehicles,
      other,
      total: totalAssets
    },
    liabilities: {
      mortgages,
      loans,
      debts,
      total: totalLiabilities
    },
    netWorth
  }
}
```

**Example:**
```
Assets:
  - Livret A: 12,500€
  - PEA: 15,000€
  - Assurance-vie: 22,000€
  - Residence: 250,000€
  Total Assets: 299,500€

Liabilities:
  - Mortgage: -180,000€
  - Car Loan: -8,000€
  Total Liabilities: -188,000€

Net Worth: 111,500€
```

---

### 5. Investment Allocation Distribution

**Purpose**: Distribute investment capacity according to user's allocation strategy

**Logic:**
```typescript
function distributeInvestmentCapacity(
  userId: string,
  capacity: number
): AllocationDistribution {
  // Get user's allocation strategy
  const allocations = getInvestmentAllocations(userId)

  // Ensure total = 100%
  const total = allocations.reduce((sum, a) => sum + a.target_percentage, 0)
  if (total !== 100) {
    throw new Error('Allocations must sum to 100%')
  }

  // Calculate amount for each category
  const distribution = allocations.map(allocation => ({
    category: allocation.category,
    targetPercentage: allocation.target_percentage,
    amount: (capacity * allocation.target_percentage) / 100
  }))

  return {
    totalCapacity: capacity,
    distribution
  }
}
```

**Example:**
```
Investment Capacity: 850€

Allocations:
  - Emergency Fund: 20% → 170€
  - PEA: 40% → 340€
  - Assurance-vie: 25% → 212.50€
  - Crypto: 10% → 85€
  - Libre: 5% → 42.50€

Total: 100% → 850€
```

---

### 6. Recurring Transaction Detection

**Purpose**: Automatically detect recurring transactions

**Algorithm:**
```typescript
function detectRecurringTransactions(userId: string): RecurringPattern[] {
  const transactions = getTransactions(userId, lastNMonths: 6)
  const patterns: RecurringPattern[] = []

  // Group by similar amount and description
  const grouped = groupBy(transactions, t =>
    `${roundToNearest(t.amount, 5)}-${normalizeDescription(t.description)}`
  )

  // Check for patterns
  for (const [key, group] of Object.entries(grouped)) {
    if (group.length < 3) continue // Need at least 3 occurrences

    // Check if intervals are consistent
    const intervals = calculateIntervals(group)
    const avgInterval = mean(intervals)
    const stdDeviation = standardDeviation(intervals)

    // If standard deviation < 3 days, it's likely recurring
    if (stdDeviation < 3) {
      let frequency = 'monthly'
      if (avgInterval >= 85 && avgInterval <= 95) frequency = 'quarterly'
      if (avgInterval >= 355 && avgInterval <= 375) frequency = 'yearly'

      patterns.push({
        transactions: group,
        frequency,
        avgAmount: mean(group.map(t => t.amount)),
        confidence: calculateConfidence(stdDeviation, group.length)
      })
    }
  }

  return patterns
}

function normalizeDescription(desc: string): string {
  // Remove dates, numbers, etc.
  return desc
    .toLowerCase()
    .replace(/\d+/g, '')
    .replace(/[^\w\s]/g, '')
    .trim()
}
```

---

### 7. Projected Net Worth

**Purpose**: Project net worth growth over time

**Formula:**
```typescript
function projectNetWorth(
  userId: string,
  years: number,
  assumptions: Assumptions
): Projection[] {
  const currentNetWorth = calculateNetWorth(userId).netWorth
  const monthlyInvestment = calculateInvestmentCapacity(userId)

  const projections: Projection[] = []
  let netWorth = currentNetWorth

  for (let month = 0; month <= years * 12; month++) {
    // Add monthly investment
    netWorth += monthlyInvestment

    // Apply returns
    const monthlyReturn = assumptions.annualReturn / 12 / 100
    netWorth *= (1 + monthlyReturn)

    // Deduct loan payments (principal reduces liabilities)
    const loanPayments = sumMonthlyLoanPayments(userId)
    // Loan payments include principal reduction (increases net worth) and interest (no effect on net worth)
    // Simplified: already counted in expenses

    if (month % 12 === 0) { // Store yearly snapshots
      projections.push({
        year: month / 12,
        netWorth: Math.round(netWorth)
      })
    }
  }

  return projections
}

interface Assumptions {
  annualReturn: number // e.g., 5% average return
  inflationRate?: number // optional, for inflation-adjusted projections
}
```

**Example:**
```
Current Net Worth: 67,500€
Monthly Investment: 850€
Annual Return: 5%

Year 0: 67,500€
Year 1: 78,500€
Year 2: 90,000€
Year 3: 102,000€
...
Year 10: 200,000€
```

---

### 8. Subscription Cost Analysis

**Purpose**: Calculate total subscription costs

**Logic:**
```typescript
function calculateSubscriptionCosts(userId: string): SubscriptionAnalysis {
  const subs = getActiveSubscriptions(userId)

  let monthlyTotal = 0
  let yearlyTotal = 0

  for (const sub of subs) {
    if (sub.frequency === 'monthly') {
      monthlyTotal += sub.amount
      yearlyTotal += sub.amount * 12
    } else if (sub.frequency === 'yearly') {
      monthlyTotal += sub.amount / 12
      yearlyTotal += sub.amount
    }
  }

  // Group by category
  const byCategory = groupBy(subs, 'category')
  const categoryTotals = Object.entries(byCategory).map(([category, items]) => ({
    category,
    count: items.length,
    monthlyTotal: sumMonthlyEquivalent(items),
    items
  }))

  return {
    monthlyTotal,
    yearlyTotal,
    count: subs.length,
    byCategory: categoryTotals,
    recommendations: generateSubscriptionRecommendations(subs, userId)
  }
}

function generateSubscriptionRecommendations(
  subs: Subscription[],
  userId: string
): Recommendation[] {
  const recommendations: Recommendation[] = []

  // Check for duplicates (e.g., two music streaming services)
  const duplicates = findDuplicateCategories(subs)
  if (duplicates.length > 0) {
    recommendations.push({
      type: 'duplicate',
      message: `Vous avez ${duplicates.length} abonnements en double`,
      items: duplicates
    })
  }

  // Check for expensive subscriptions relative to budget
  const budget = calculateMonthlyBudget(userId)
  const subsPercentage = (calculateSubscriptionCosts(userId).monthlyTotal / budget) * 100
  if (subsPercentage > 15) {
    recommendations.push({
      type: 'high_ratio',
      message: `Vos abonnements représentent ${subsPercentage.toFixed(0)}% de votre budget`,
      suggestion: 'Envisagez de réduire certains abonnements'
    })
  }

  return recommendations
}
```

---

### 9. Goal Progress Tracking

**Purpose**: Track progress toward investment goals

**Logic:**
```typescript
function calculateGoalProgress(goalId: string): GoalProgress {
  const goal = getInvestmentGoal(goalId)

  const progressPercentage = (goal.current_amount / goal.target_amount) * 100

  let status: 'on_track' | 'behind' | 'ahead' | 'completed'

  if (goal.current_amount >= goal.target_amount) {
    status = 'completed'
  } else if (goal.target_date) {
    const monthsRemaining = differenceInMonths(goal.target_date, new Date())
    const monthlyNeeded = (goal.target_amount - goal.current_amount) / monthsRemaining

    // Get user's investment capacity
    const capacity = calculateInvestmentCapacity(goal.user_id)

    if (monthlyNeeded <= capacity * 0.5) status = 'ahead'
    else if (monthlyNeeded <= capacity) status = 'on_track'
    else status = 'behind'
  } else {
    status = 'on_track' // No deadline, just tracking
  }

  return {
    goalId,
    currentAmount: goal.current_amount,
    targetAmount: goal.target_amount,
    progressPercentage,
    status,
    monthlyNeeded: goal.target_date ?
      (goal.target_amount - goal.current_amount) /
      differenceInMonths(goal.target_date, new Date()) : null
  }
}
```

---

## Helper Functions

### Sum Transactions
```typescript
function sumTransactions(
  businessId: string,
  type: 'revenue' | 'expense',
  month: Date
): number {
  return db
    .select({ sum: sql`COALESCE(SUM(amount), 0)` })
    .from(transactions)
    .where(and(
      eq(transactions.businessId, businessId),
      eq(transactions.type, type),
      sql`DATE_TRUNC('month', date) = DATE_TRUNC('month', ${month})`
    ))
    .then(r => r[0].sum)
}
```

### Calculate Average Expenses
```typescript
function calculateAverageExpenses(
  userId: string,
  lastNMonths: number
): number {
  const startDate = subMonths(new Date(), lastNMonths)

  const total = db
    .select({ sum: sql`COALESCE(SUM(amount), 0)` })
    .from(personalTransactions)
    .where(and(
      eq(personalTransactions.userId, userId),
      eq(personalTransactions.type, 'expense'),
      gte(personalTransactions.date, startDate)
    ))
    .then(r => r[0].sum)

  return total / lastNMonths
}
```

---

## Validation Rules

### Transaction Validation
```typescript
function validateTransaction(transaction: Transaction): ValidationResult {
  const errors: string[] = []

  // Amount must be positive
  if (transaction.amount <= 0) {
    errors.push('Amount must be greater than 0')
  }

  // Amount should not be abnormally large
  if (transaction.amount > 100000) {
    errors.push('Amount seems unusually large. Please confirm.')
  }

  // Date should not be in future
  if (isAfter(transaction.date, new Date())) {
    errors.push('Date cannot be in the future')
  }

  // Category must be valid
  const validCategories = getValidCategories(transaction.type)
  if (!validCategories.includes(transaction.category)) {
    errors.push(`Invalid category: ${transaction.category}`)
  }

  return {
    valid: errors.length === 0,
    errors
  }
}
```

---

**Last Updated**: 2025-12-25
