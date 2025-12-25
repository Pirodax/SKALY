# SKALY - Context for Claude Code

> **This file provides essential context for Claude Code and AI agents working on this project.**

## Project Overview

**SKALY** is an all-in-one financial management platform specifically designed for freelancers, independent contractors, and micro-entrepreneurs. Unlike generic financial apps (Finary, Bankin'), SKALY understands the unique reality of people juggling personal and business income, often managing multiple businesses in parallel.

## Tech Stack

- **Framework**: Next.js 16 (App Router)
- **Frontend**: React 19, TypeScript
- **Styling**: Tailwind CSS 4
- **Backend/Database**: Supabase (PostgreSQL + Auth + Storage)
- **Deployment**: TBD (Vercel recommended)

## Project Structure

```
skaly_app/
├── app/                    # Next.js App Router pages
├── docs/                   # Complete project documentation
│   ├── 00_INDEX.md        # Documentation navigation
│   ├── 01_VISION_PRD.md   # Vision & Product Requirements
│   ├── 02_USER_FLOWS.md   # User flows and scenarios
│   ├── 03_DATA_MODEL.md   # Database schema & relationships
│   ├── 04_CALCULATIONS_RULES.md  # Business logic & formulas
│   ├── 05_UI_NAVIGATION.md       # UI/UX specifications
│   ├── 06_API_CONTRACTS.md       # API endpoints & contracts
│   ├── 07_SECURITY_PRIVACY.md    # Security & privacy rules
│   ├── 08_ROADMAP.md             # Development roadmap
│   └── 09_GLOSSARY.md            # Terms & definitions
├── public/                # Static assets
├── supabase/              # Supabase config & migrations
└── LLM_CONTEXT.md         # This file

```

## Key Concepts

### Multi-Business Architecture
- Users can manage multiple businesses simultaneously
- Each business has separate revenue/expense tracking
- Clear separation between personal and professional finances

### Core Sections
1. **Overview**: Dashboard with global financial health
2. **Personnel**: Personal income, expenses, subscriptions
3. **Business**: Multi-business tracking with P&L per business
4. **Investissement**: Automatic investment capacity calculation
5. **Patrimoine**: Net worth tracking (assets - liabilities)
6. **Settings**: User preferences and configurations

### Unique Value Proposition
- **Personal/Business Separation**: Clear distinction unlike generic apps
- **Multi-Business Support**: Track multiple income streams separately
- **Smart Investment Calculation**: Automatic capacity based on income & provisions
- **Tax Provisions**: Auto-calculate URSSAF, income tax provisions
- **Freelancer-Focused**: Built specifically for independent workers

## Development Guidelines

### Code Style
- Use TypeScript strict mode
- Functional components with hooks
- Server components by default (Next.js App Router)
- Client components only when needed (interactivity, hooks)
- Tailwind CSS for all styling (no CSS modules)

### File Naming
- Components: PascalCase (e.g., `BusinessCard.tsx`)
- Utils/helpers: camelCase (e.g., `calculateInvestmentCapacity.ts`)
- Pages: lowercase with hyphens (e.g., `app/business/[id]/page.tsx`)

### Component Structure
```typescript
// Server Component (default)
export default async function BusinessPage({ params }: { params: { id: string } }) {
  // Server-side data fetching
  const business = await getBusinessById(params.id);
  return <BusinessDetails business={business} />;
}

// Client Component (when needed)
'use client'

import { useState } from 'react'

export default function BusinessForm() {
  const [formData, setFormData] = useState({...})
  // Interactive logic
}
```

### Database Access
- Use Supabase client for database operations
- Server-side: `@supabase/ssr` with cookies
- Client-side: `@supabase/supabase-js` (authenticated)
- Always use Row Level Security (RLS)

### State Management
- Server state: React Server Components + Supabase
- Client state: React hooks (useState, useReducer)
- Complex state: Consider Zustand or Jotai if needed
- No Redux (overkill for this project)

## Important Calculation Formulas

### Investment Capacity
```
Monthly Investment Capacity =
  Recurring Personal Income
  + Net Business Profits (after provisions)
  - Recurring Personal Expenses
  - Security Provision (configurable, default: 1 month expenses)
```

### Net Business Profit
```
Net Business Profit =
  Business Revenue
  - Business Expenses
  - URSSAF Provisions (% based on legal status)
  - Income Tax Provisions (% based on tax bracket)
  - CFE (annual, provisioned monthly)
```

### Net Worth
```
Net Worth = Total Assets - Total Liabilities

Assets:
  - Liquid Savings (Livret A, LDDS, checking)
  - Investments (PEA, Life Insurance, Crypto)
  - Real Estate (estimated value)
  - Other Assets (vehicles, valuables)

Liabilities:
  - Mortgages
  - Loans
  - Debts
```

## Common Tasks & Commands

### Development
```bash
npm run dev          # Start dev server (localhost:3000)
npm run build        # Build for production
npm run lint         # Run ESLint
```

### Supabase
```bash
npx supabase start   # Start local Supabase
npx supabase stop    # Stop local Supabase
npx supabase db reset  # Reset local DB
```

## Key Files to Read First

When working on specific features, read these docs first:
- New feature: `docs/01_VISION_PRD.md` + `docs/08_ROADMAP.md`
- Database changes: `docs/03_DATA_MODEL.md`
- Business logic: `docs/04_CALCULATIONS_RULES.md`
- API work: `docs/06_API_CONTRACTS.md`
- UI changes: `docs/05_UI_NAVIGATION.md`

## Testing Strategy

### Unit Tests
- Business logic calculations (formulas)
- Utility functions
- Data transformations

### Integration Tests
- API routes
- Database operations
- Authentication flows

### E2E Tests
- Critical user flows:
  - User registration → Business creation → First transaction
  - Investment capacity calculation
  - Multi-business dashboard

## Security Considerations

- **RLS**: All Supabase tables must have Row Level Security
- **Auth**: Supabase Auth with email/password + social providers
- **Data Isolation**: Users can only access their own data
- **Sensitive Data**: Never log financial data, use Supabase vault for secrets
- **GDPR**: Export/delete user data capabilities required

## Performance Goals

- Initial page load: < 2s
- Time to Interactive: < 3s
- Lighthouse score: > 90
- Bundle size: Monitor with `@next/bundle-analyzer`

## Deployment

- **Production**: Vercel (recommended for Next.js)
- **Database**: Supabase Cloud
- **Environment Variables**: Managed in Vercel/Supabase dashboards
- **CI/CD**: GitHub Actions for tests before merge

## Useful Claude Code Skills

This project includes custom skills:
- `/new-feature`: Scaffold a new feature with all necessary files
- `/business-logic`: Add a new business calculation rule
- `/db-migration`: Create a Supabase migration
- `/api-route`: Generate a new API route with types

## Getting Help

- Documentation: Start with `docs/00_INDEX.md`
- Database schema: `supabase/migrations/`
- Business rules: `docs/04_CALCULATIONS_RULES.md`
- Ask questions with context from relevant docs

## Current Status

**Phase**: Initial Development
**Version**: 0.1.0
**Last Updated**: 2025-12-25

See `docs/08_ROADMAP.md` for detailed development phases and priorities.
