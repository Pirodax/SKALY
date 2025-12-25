# API Route Skill

## Description
Generate a complete Next.js API route with authentication, validation, error handling, and documentation.

## Usage
```
/api-route [endpoint-path] [method]
```

Example:
```
/api-route businesses POST
/api-route businesses/:id GET
```

## What This Skill Does

1. **Asks about the endpoint** requirements
2. **Reviews API contracts** documentation
3. **Creates the route file** with full implementation
4. **Adds validation schema**
5. **Implements authentication**
6. **Adds error handling**
7. **Updates API documentation**

## Process

### Step 1: Gather Requirements
Ask the user:
- Endpoint purpose
- HTTP method (GET, POST, PUT, DELETE)
- Request parameters (query, path, body)
- Response format
- Authentication required? (usually yes)
- Database tables involved
- Special business logic?

### Step 2: Review API Contracts
Read `docs/06_API_CONTRACTS.md` to:
- Follow existing patterns
- Match response formats
- Use consistent error handling
- Follow naming conventions

### Step 3: Create Route File

File location: `app/api/[endpoint]/route.ts`

For dynamic routes: `app/api/[endpoint]/[id]/route.ts`

### Step 4: Route Implementation Template

#### GET Request (Read)
```typescript
import { NextRequest, NextResponse } from 'next/server'
import { createServerClient } from '@/lib/supabase/server'

export async function GET(
  request: NextRequest,
  { params }: { params: { id?: string } }
) {
  try {
    // 1. Authentication
    const supabase = createServerClient()
    const { data: { user }, error: authError } = await supabase.auth.getUser()

    if (authError || !user) {
      return NextResponse.json(
        { error: 'Unauthorized', message: 'Authentication required' },
        { status: 401 }
      )
    }

    // 2. Get query parameters (for listing)
    const { searchParams } = new URL(request.url)
    const page = parseInt(searchParams.get('page') || '1')
    const limit = parseInt(searchParams.get('limit') || '50')
    const filter = searchParams.get('filter')

    // 3. Database query with RLS
    let query = supabase
      .from('[table_name]')
      .select('*', { count: 'exact' })
      .eq('user_id', user.id)

    // Add filters
    if (filter) {
      query = query.eq('category', filter)
    }

    // Pagination
    const offset = (page - 1) * limit
    query = query
      .range(offset, offset + limit - 1)
      .order('created_at', { ascending: false })

    const { data, error, count } = await query

    if (error) {
      console.error('Database error:', error)
      return NextResponse.json(
        { error: 'Database error', message: error.message },
        { status: 500 }
      )
    }

    // 4. Format response
    return NextResponse.json({
      data,
      pagination: {
        page,
        limit,
        total: count || 0,
        totalPages: Math.ceil((count || 0) / limit)
      }
    })
  } catch (error) {
    console.error('API Error:', error)
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

#### GET Request (Single Item)
```typescript
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    // Authentication
    const supabase = createServerClient()
    const { data: { user }, error: authError } = await supabase.auth.getUser()

    if (authError || !user) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      )
    }

    // Get single item (RLS ensures user can only get their own)
    const { data, error } = await supabase
      .from('[table_name]')
      .select('*')
      .eq('id', params.id)
      .eq('user_id', user.id)
      .single()

    if (error) {
      if (error.code === 'PGRST116') {
        return NextResponse.json(
          { error: 'Not found' },
          { status: 404 }
        )
      }

      console.error('Database error:', error)
      return NextResponse.json(
        { error: 'Database error' },
        { status: 500 }
      )
    }

    return NextResponse.json(data)
  } catch (error) {
    console.error('API Error:', error)
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

#### POST Request (Create)
```typescript
import { z } from 'zod'

// Validation schema
const createSchema = z.object({
  [field]: z.string().min(1).max(100),
  [field]: z.number().positive(),
  [field]: z.enum(['option1', 'option2']),
  [field]: z.date().optional(),
})

export async function POST(request: NextRequest) {
  try {
    // 1. Authentication
    const supabase = createServerClient()
    const { data: { user }, error: authError } = await supabase.auth.getUser()

    if (authError || !user) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      )
    }

    // 2. Parse and validate request body
    const body = await request.json()
    const result = createSchema.safeParse(body)

    if (!result.success) {
      return NextResponse.json(
        {
          error: 'Validation failed',
          details: result.error.format()
        },
        { status: 422 }
      )
    }

    const validatedData = result.data

    // 3. Business logic validation (if needed)
    // Example: Check limits, verify relationships, etc.

    // 4. Insert into database
    const { data, error } = await supabase
      .from('[table_name]')
      .insert([
        {
          ...validatedData,
          user_id: user.id
        }
      ])
      .select()
      .single()

    if (error) {
      console.error('Database error:', error)
      return NextResponse.json(
        { error: 'Failed to create', message: error.message },
        { status: 500 }
      )
    }

    // 5. Return created resource
    return NextResponse.json(data, { status: 201 })
  } catch (error) {
    console.error('API Error:', error)
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

#### PUT Request (Update)
```typescript
const updateSchema = z.object({
  [field]: z.string().min(1).max(100).optional(),
  [field]: z.number().positive().optional(),
}).partial() // All fields optional for updates

export async function PUT(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    // Authentication
    const supabase = createServerClient()
    const { data: { user }, error: authError } = await supabase.auth.getUser()

    if (authError || !user) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      )
    }

    // Validation
    const body = await request.json()
    const result = updateSchema.safeParse(body)

    if (!result.success) {
      return NextResponse.json(
        { error: 'Validation failed', details: result.error.format() },
        { status: 422 }
      )
    }

    // Update (RLS ensures user can only update their own)
    const { data, error } = await supabase
      .from('[table_name]')
      .update(result.data)
      .eq('id', params.id)
      .eq('user_id', user.id)
      .select()
      .single()

    if (error) {
      if (error.code === 'PGRST116') {
        return NextResponse.json(
          { error: 'Not found' },
          { status: 404 }
        )
      }

      console.error('Database error:', error)
      return NextResponse.json(
        { error: 'Update failed' },
        { status: 500 }
      )
    }

    return NextResponse.json(data)
  } catch (error) {
    console.error('API Error:', error)
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

#### DELETE Request
```typescript
export async function DELETE(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  try {
    // Authentication
    const supabase = createServerClient()
    const { data: { user }, error: authError } = await supabase.auth.getUser()

    if (authError || !user) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      )
    }

    // Soft delete (recommended) or hard delete
    const { error } = await supabase
      .from('[table_name]')
      .update({ is_active: false }) // Soft delete
      // .delete() // Hard delete
      .eq('id', params.id)
      .eq('user_id', user.id)

    if (error) {
      console.error('Database error:', error)
      return NextResponse.json(
        { error: 'Delete failed' },
        { status: 500 }
      )
    }

    return NextResponse.json(
      { success: true, message: 'Deleted successfully' }
    )
  } catch (error) {
    console.error('API Error:', error)
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

### Step 5: Update Documentation

Add to `docs/06_API_CONTRACTS.md`:

```markdown
#### [METHOD] `/api/[endpoint]`

**Description**: [What this endpoint does]

**Authentication**: Required

**Request:**
\`\`\`json
{
  "[field]": "[value]",
  "[field]": [value]
}
\`\`\`

**Validation:**
- `[field]`: Required, [constraints]
- `[field]`: Optional, [constraints]

**Response (Success):**
\`\`\`json
{
  "id": "uuid",
  "[field]": "[value]",
  "createdAt": "2025-12-25T14:32:00Z"
}
\`\`\`

**Response (Error):**
\`\`\`json
{
  "error": "Validation failed",
  "details": {
    "[field]": ["Error message"]
  },
  "statusCode": 422
}
\`\`\`

**Status Codes:**
- 200: Success
- 201: Created
- 400: Bad Request
- 401: Unauthorized
- 404: Not Found
- 422: Validation Error
- 500: Server Error

---
```

## Validation Patterns

### Common Zod Schemas
```typescript
// String
z.string()
  .min(1, 'Required')
  .max(100, 'Too long')
  .email('Invalid email')
  .url('Invalid URL')

// Number
z.number()
  .positive('Must be positive')
  .int('Must be integer')
  .min(0)
  .max(1000000)

// Date
z.string().datetime()
// or
z.date()
  .max(new Date(), 'Cannot be in future')

// Enum
z.enum(['option1', 'option2', 'option3'])

// Boolean
z.boolean()

// Optional
z.string().optional()

// Nullable
z.string().nullable()

// Transform
z.string().transform(s => s.toLowerCase())

// Refinement
z.number().refine(n => n % 2 === 0, 'Must be even')
```

## Error Handling Checklist

- [ ] Authentication checked
- [ ] Input validated with Zod
- [ ] Database errors caught
- [ ] 404 handled for not found
- [ ] 422 returned for validation errors
- [ ] 500 for unexpected errors
- [ ] Errors logged (without sensitive data)
- [ ] Consistent error format

## Testing Checklist

- [ ] Test with valid data
- [ ] Test with invalid data (validation)
- [ ] Test without authentication
- [ ] Test with wrong user (shouldn't access others' data)
- [ ] Test not found scenarios
- [ ] Test edge cases
- [ ] Test pagination (if applicable)

## Example Usage

```
User: /api-route businesses POST

Claude: I'll create a POST endpoint to create a new business.

Questions:
1. Required fields:
   - name (string, 1-100 chars)
   - type (enum: freelance, ecommerce, saas, etc.)
   - legalStatus (enum: micro_entreprise, sasu, etc.)
   - urssafRate (number, 0-100)

2. Optional fields:
   - cfeAnnual (number, default 0)
   - createdDate (date)

3. Business logic:
   - Check unique name per user
   - Validate URSSAF rate based on legal status

[Creates the complete route file with all validation and error handling]

Created: app/api/businesses/route.ts

The endpoint is ready! Test with:

curl -X POST http://localhost:3000/api/businesses \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Freelance Design",
    "type": "freelance",
    "legalStatus": "micro_entreprise",
    "urssafRate": 21.2
  }'
```

## Best Practices

- Always authenticate (except auth routes)
- Use RLS for automatic user filtering
- Validate all inputs with Zod
- Use TypeScript for type safety
- Handle all error cases
- Log errors (but not sensitive data)
- Return consistent response formats
- Use proper HTTP status codes
- Add rate limiting for production
- Document all endpoints
