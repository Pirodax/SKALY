# Database Migration Skill

## Description
Create a Supabase database migration with proper structure, RLS policies, and indexes.

## Usage
```
/db-migration [description]
```

## What This Skill Does

1. **Asks about the database changes** needed
2. **Reviews data model** documentation
3. **Creates migration file** with proper naming
4. **Adds RLS policies** for security
5. **Adds indexes** for performance
6. **Provides rollback** instructions
7. **Updates documentation**

## Process

### Step 1: Gather Requirements
Ask the user:
- What's changing? (new table, new column, modify existing, etc.)
- Table name and purpose
- Columns needed (name, type, constraints)
- Relationships to other tables?
- Who should have access? (RLS policies)
- Indexes needed for performance?

### Step 2: Review Data Model
Read `docs/03_DATA_MODEL.md` to:
- Ensure naming consistency
- Check for existing similar structures
- Understand relationships
- Follow established patterns

### Step 3: Create Migration File

File name format: `YYYYMMDDHHMMSS_[description].sql`

```bash
# Generate timestamp
TIMESTAMP=$(date +%Y%m%d%H%M%S)

# Create file
touch supabase/migrations/${TIMESTAMP}_[description].sql
```

### Step 4: Migration Content

#### New Table Template
```sql
-- Create table: [table_name]
-- Purpose: [description]

CREATE TABLE IF NOT EXISTS [table_name] (
  -- Primary key
  id uuid PRIMARY KEY DEFAULT uuid_generate_v4(),

  -- Foreign keys
  user_id uuid REFERENCES profiles(id) ON DELETE CASCADE NOT NULL,
  [other_fk] uuid REFERENCES [other_table](id) ON DELETE [CASCADE|SET NULL],

  -- Data columns
  [column_name] [data_type] [constraints],

  -- Timestamps
  created_at timestamptz DEFAULT now() NOT NULL,
  updated_at timestamptz DEFAULT now() NOT NULL
);

-- Enable Row Level Security
ALTER TABLE [table_name] ENABLE ROW LEVEL SECURITY;

-- RLS Policies
CREATE POLICY "Users can view own [table_name]"
  ON [table_name]
  FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can insert own [table_name]"
  ON [table_name]
  FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own [table_name]"
  ON [table_name]
  FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete own [table_name]"
  ON [table_name]
  FOR DELETE
  USING (auth.uid() = user_id);

-- Indexes
CREATE INDEX idx_[table_name]_user_id
  ON [table_name](user_id);

CREATE INDEX idx_[table_name]_created_at
  ON [table_name](created_at DESC);

-- Add other indexes for frequently queried columns
[IF APPLICABLE]:
CREATE INDEX idx_[table_name]_[column]
  ON [table_name]([column]);

-- Unique constraints (if needed)
[IF APPLICABLE]:
CREATE UNIQUE INDEX idx_[table_name]_unique_[column]
  ON [table_name](user_id, [column])
  WHERE [condition];

-- Triggers for updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = now();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_[table_name]_updated_at
  BEFORE UPDATE ON [table_name]
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

-- Comments
COMMENT ON TABLE [table_name] IS '[Description of table purpose]';
COMMENT ON COLUMN [table_name].[column] IS '[Description]';
```

#### Add Column Template
```sql
-- Add column [column_name] to [table_name]
-- Purpose: [description]

ALTER TABLE [table_name]
  ADD COLUMN [column_name] [data_type] [constraints];

-- Update existing rows if needed
UPDATE [table_name]
  SET [column_name] = [default_value]
  WHERE [column_name] IS NULL;

-- Add constraint after backfilling
ALTER TABLE [table_name]
  ALTER COLUMN [column_name] SET NOT NULL;

-- Add index if needed
CREATE INDEX idx_[table_name]_[column_name]
  ON [table_name]([column_name]);

-- Comment
COMMENT ON COLUMN [table_name].[column_name] IS '[Description]';
```

#### Modify Column Template
```sql
-- Modify column [column_name] in [table_name]
-- Change: [description of change]

ALTER TABLE [table_name]
  ALTER COLUMN [column_name] TYPE [new_type];
  -- or
  ALTER COLUMN [column_name] SET NOT NULL;
  -- or
  ALTER COLUMN [column_name] SET DEFAULT [value];
```

#### Create View Template
```sql
-- Create view: [view_name]
-- Purpose: [description]

CREATE OR REPLACE VIEW [view_name] AS
SELECT
  [columns]
FROM [table]
WHERE [conditions]
GROUP BY [columns]
ORDER BY [columns];

-- Or materialized view for performance
CREATE MATERIALIZED VIEW [view_name] AS
SELECT
  [columns]
FROM [table]
WHERE [conditions]
GROUP BY [columns];

-- Index on materialized view
CREATE UNIQUE INDEX idx_[view_name]_[column]
  ON [view_name]([column]);

-- Refresh function
CREATE OR REPLACE FUNCTION refresh_[view_name]()
RETURNS void AS $$
BEGIN
  REFRESH MATERIALIZED VIEW CONCURRENTLY [view_name];
END;
$$ LANGUAGE plpgsql;

-- Comment
COMMENT ON VIEW [view_name] IS '[Description]';
```

### Step 5: Rollback Script

Create rollback instructions at top of file:

```sql
-- Rollback instructions:
-- To undo this migration, run:
-- DROP TABLE IF EXISTS [table_name] CASCADE;
-- or
-- ALTER TABLE [table_name] DROP COLUMN [column_name];

-- Migration starts here:
-- [migration content]
```

### Step 6: Test Migration

```bash
# Test locally
npx supabase db reset

# Verify
npx supabase db dump --schema public

# If good, apply to production (careful!)
npx supabase db push
```

### Step 7: Update Documentation

Add to `docs/03_DATA_MODEL.md`:

```markdown
### [Table Number]. [table_name]

**Purpose**: [Description]

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | uuid | PK, DEFAULT uuid_generate_v4() | Primary key |
| user_id | uuid | FK(profiles), NOT NULL | Owner |
| [column] | [type] | [constraints] | [description] |
| created_at | timestamptz | DEFAULT now() | Record creation |
| updated_at | timestamptz | DEFAULT now() | Last update |

**Indexes:**
- PRIMARY KEY (id)
- INDEX (user_id)
- INDEX (created_at DESC)

**RLS Policies:**
\`\`\`sql
CREATE POLICY "Users can view own [table]" ON [table]
  FOR SELECT USING (auth.uid() = user_id);
-- [other policies]
\`\`\`

**Relationships:**
- `user_id` → `profiles(id)` (Many to one)
- `[fk]` → `[other_table](id)` (Many to one)

---
```

## Common Data Types

| Type | Use Case | Example |
|------|----------|---------|
| `uuid` | IDs | DEFAULT uuid_generate_v4() |
| `text` | Strings | Names, descriptions |
| `numeric(10,2)` | Money | 12345.67 (10 digits, 2 decimals) |
| `boolean` | Flags | true/false |
| `date` | Dates | 2025-12-25 |
| `timestamptz` | Timestamps | 2025-12-25T14:32:00Z |
| `integer` | Whole numbers | 1, 2, 3 |
| `jsonb` | JSON data | { "key": "value" } |

## Common Constraints

- `NOT NULL` - Value required
- `UNIQUE` - No duplicates
- `CHECK (condition)` - Custom validation
- `DEFAULT value` - Default value
- `REFERENCES table(column)` - Foreign key

## Performance Tips

1. **Index foreign keys** - Always index FK columns
2. **Index WHERE clauses** - Index columns used in WHERE
3. **Composite indexes** - For multi-column queries
4. **Partial indexes** - For filtered queries
5. **Avoid over-indexing** - Too many slow down writes

## Security Checklist

- [ ] RLS enabled on table
- [ ] SELECT policy added
- [ ] INSERT policy added (if needed)
- [ ] UPDATE policy added (if needed)
- [ ] DELETE policy added (if needed)
- [ ] Policies tested
- [ ] No way to access other users' data

## Example Usage

```
User: /db-migration add-recurring-transactions

Claude: I'll help you create a migration for recurring transactions.

Questions:
1. Should this be a new table or add columns to existing transactions table?
   Recommendation: New table to track recurring patterns separately

2. What data should we store?
   - Original transaction ID
   - Recurrence frequency (monthly, quarterly, yearly)
   - Next occurrence date
   - Is active?

3. Should users be able to edit recurring patterns?
   Yes - will add UPDATE policy

[Creates migration file with all necessary SQL]

Created: supabase/migrations/20251225143000_add_recurring_transactions.sql

Next steps:
1. Review the migration file
2. Test locally: npx supabase db reset
3. Update docs/03_DATA_MODEL.md (I can do this)
4. Apply to production when ready: npx supabase db push
```

## Best Practices

- Use descriptive migration names
- One logical change per migration
- Add rollback instructions
- Test locally before production
- Update documentation immediately
- Use transactions for complex migrations
- Add helpful comments
- Follow naming conventions
