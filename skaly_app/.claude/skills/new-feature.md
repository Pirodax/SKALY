# New Feature Skill

## Description
Scaffold a complete new feature for SKALY with all necessary files and best practices.

## Usage
```
/new-feature [feature-name]
```

## What This Skill Does

1. **Asks clarifying questions** about the feature
2. **Reads relevant documentation** from docs/
3. **Creates necessary files**:
   - API routes (if needed)
   - Components
   - Types
   - Utilities
   - Tests
4. **Updates documentation** if needed
5. **Provides next steps** for implementation

## Process

### Step 1: Gather Information
Ask the user:
- Feature name and description
- Which section (Personal, Business, Investment, Net Worth, Dashboard)
- Data model changes needed?
- New API endpoints needed?
- Calculations involved?

### Step 2: Review Documentation
Read:
- `docs/01_VISION_PRD.md` - Ensure feature aligns with vision
- `docs/03_DATA_MODEL.md` - Check if DB changes needed
- `docs/04_CALCULATIONS_RULES.md` - If financial calculations
- `docs/05_UI_NAVIGATION.md` - UI patterns to follow
- `docs/06_API_CONTRACTS.md` - API conventions

### Step 3: Create Files

#### Database Migration (if needed)
```bash
# Create migration file
touch supabase/migrations/YYYYMMDDHHMMSS_[feature_name].sql
```

Content template:
```sql
-- Create table/column for [feature]
CREATE TABLE IF NOT EXISTS [table_name] (
  id uuid PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id uuid REFERENCES profiles(id) NOT NULL,
  -- other columns
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);

-- RLS policies
ALTER TABLE [table_name] ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own [table_name]"
  ON [table_name]
  FOR SELECT
  USING (auth.uid() = user_id);

-- Add indexes
CREATE INDEX idx_[table_name]_user_id ON [table_name](user_id);
```

#### API Routes
```bash
# Create API route
touch app/api/[feature]/route.ts
```

Use the api_route template from .clauderc

#### Components
```bash
# Create components
mkdir -p components/features/[feature]
touch components/features/[feature]/[FeatureName].tsx
touch components/features/[feature]/[FeatureName]Form.tsx
touch components/features/[feature]/[FeatureName]List.tsx
```

#### Types
```bash
# Create types
touch types/[feature].ts
```

#### Calculations (if financial logic)
```bash
# Create calculation utilities
touch lib/calculations/calculate[FeatureName].ts
```

#### Tests
```bash
# Create tests
mkdir -p __tests__/[feature]
touch __tests__/[feature]/calculations.test.ts
touch __tests__/[feature]/api.test.ts
```

### Step 4: Update Documentation

Add to relevant docs:
- `docs/03_DATA_MODEL.md` - New tables/columns
- `docs/04_CALCULATIONS_RULES.md` - New formulas
- `docs/06_API_CONTRACTS.md` - New endpoints

### Step 5: Implementation Checklist

Create a checklist for the user:
- [ ] Database migration created and tested locally
- [ ] RLS policies implemented
- [ ] API routes implemented with validation
- [ ] Components created and styled
- [ ] Calculations tested
- [ ] Integration tests added
- [ ] Documentation updated
- [ ] Error handling implemented
- [ ] Loading states added
- [ ] Accessibility checked

## Example Usage

```
User: /new-feature subscription-analysis

Claude: I'll help you create the subscription analysis feature. Let me ask a few questions:

1. Which section should this feature appear in? (Personal, Business, or both)
2. Should this calculate total subscription costs and provide recommendations?
3. Do we need a new database table or use existing subscriptions table?

[After gathering info, creates all necessary files and provides implementation guide]
```

## Best Practices

- Follow TypeScript strict mode
- Use Server Components by default
- Add proper validation with Zod
- Implement RLS for all database access
- Test financial calculations thoroughly
- Add loading and error states
- Follow existing UI patterns
- Document all new calculations
