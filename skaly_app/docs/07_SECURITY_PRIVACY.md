# SKALY - Security & Privacy Guidelines

## Authentication & Authorization

### Supabase Auth

**Authentication Methods:**
- Email/Password (primary)
- Google OAuth (future)
- GitHub OAuth (future)

**Password Requirements:**
- Minimum 8 characters
- At least 1 uppercase letter
- At least 1 lowercase letter
- At least 1 number
- At least 1 special character

**Session Management:**
- JWT tokens stored in HTTP-only cookies
- Token expiration: 1 hour
- Refresh token: 7 days
- Automatic token refresh before expiration

### Authorization

**Row Level Security (RLS):**
All database tables MUST have RLS enabled.

**Policy Pattern:**
```sql
-- Users can only access their own data
CREATE POLICY "policy_name" ON table_name
  FOR SELECT
  USING (auth.uid() = user_id);
```

**API Route Protection:**
```typescript
import { createServerClient } from '@/lib/supabase/server'

export async function GET(request: Request) {
  const supabase = createServerClient()

  const { data: { user }, error } = await supabase.auth.getUser()

  if (error || !user) {
    return Response.json({ error: 'Unauthorized' }, { status: 401 })
  }

  // Proceed with authenticated user
}
```

---

## Data Encryption

### At Rest
- **Database**: Supabase provides encryption at rest (AES-256)
- **Backups**: Encrypted with the same key
- **File Storage**: Supabase Storage encryption enabled

### In Transit
- **HTTPS Only**: All connections use TLS 1.3
- **HSTS**: HTTP Strict Transport Security enabled
- **Certificate**: Let's Encrypt with auto-renewal

### Sensitive Data Handling

**Never log:**
- Passwords
- Financial account numbers
- Full credit card numbers
- Authentication tokens
- Personal identification numbers

**Mask in logs:**
```typescript
// BAD
console.log('Transaction:', transaction)

// GOOD
console.log('Transaction:', {
  id: transaction.id,
  amount: '***', // Mask amount
  userId: maskUserId(transaction.userId)
})
```

---

## Input Validation

### Server-Side Validation (Required)

**Validation Library:** Zod

```typescript
import { z } from 'zod'

const transactionSchema = z.object({
  type: z.enum(['income', 'expense']),
  amount: z.number().positive().max(1000000),
  category: z.string().min(1).max(100),
  description: z.string().max(500).optional(),
  date: z.date().max(new Date())
})

// Validate
const result = transactionSchema.safeParse(data)
if (!result.success) {
  return Response.json(
    { error: 'Validation failed', details: result.error },
    { status: 422 }
  )
}
```

### SQL Injection Prevention

**Always use parameterized queries:**
```typescript
// BAD - SQL Injection vulnerable
const { data } = await supabase
  .from('transactions')
  .select('*')
  .where(`user_id = '${userId}'`) // NEVER DO THIS

// GOOD - Parameterized
const { data } = await supabase
  .from('transactions')
  .select('*')
  .eq('user_id', userId)
```

### XSS Prevention

**Sanitize user input:**
```typescript
import DOMPurify from 'isomorphic-dompurify'

const sanitized = DOMPurify.sanitize(userInput)
```

**React auto-escapes by default:**
```tsx
// Safe - React auto-escapes
<div>{userInput}</div>

// Dangerous - Only use with trusted content
<div dangerouslySetInnerHTML={{ __html: sanitized }} />
```

---

## CSRF Protection

**Next.js built-in protection:**
- Uses SameSite cookies
- Origin header validation

**For sensitive actions, add CSRF token:**
```typescript
import { getToken } from '@/lib/csrf'

// Generate token
const token = await getToken(request)

// Validate token
if (requestToken !== token) {
  return Response.json({ error: 'Invalid CSRF token' }, { status: 403 })
}
```

---

## GDPR Compliance

### Data Collection

**Principle of Data Minimization:**
Only collect data necessary for the service.

**Collected Data:**
- Account: Email, first name
- Financial: Transactions, budgets, goals
- Usage: Analytics (anonymized)

**NOT Collected:**
- Full name (only first name)
- Address (unless needed for specific feature)
- Phone number
- Social security number
- Bank account credentials

### User Rights

#### Right to Access
**Endpoint:** `GET /api/user/data-export`

**Response:** ZIP file containing:
- JSON export of all user data
- Readable HTML summary

#### Right to Rectification
Users can update their data through the Settings page.

#### Right to Erasure (Right to be Forgotten)
**Endpoint:** `DELETE /api/user/account`

**Process:**
1. User requests deletion
2. Confirmation email sent
3. 30-day grace period
4. Hard delete all user data
5. Confirmation email

**Implementation:**
```typescript
async function deleteUserData(userId: string) {
  // Delete in order (respecting foreign keys)
  await supabase.from('transactions').delete().eq('user_id', userId)
  await supabase.from('businesses').delete().eq('user_id', userId)
  await supabase.from('assets').delete().eq('user_id', userId)
  await supabase.from('liabilities').delete().eq('user_id', userId)
  await supabase.from('budgets').delete().eq('user_id', userId)
  await supabase.from('subscriptions').delete().eq('user_id', userId)
  await supabase.from('investment_goals').delete().eq('user_id', userId)
  await supabase.from('investment_allocations').delete().eq('user_id', userId)
  await supabase.from('profiles').delete().eq('id', userId)

  // Delete auth user
  await supabase.auth.admin.deleteUser(userId)
}
```

#### Right to Data Portability
Same as Right to Access - exportable JSON format.

#### Right to Object
Users can opt-out of analytics.

### Privacy Policy

**Must include:**
- What data we collect
- Why we collect it
- How we use it
- How long we retain it
- User rights
- Contact information

**Location:** `/legal/privacy-policy`

---

## Security Best Practices

### Environment Variables

**Never commit secrets:**
```bash
# .env.local (gitignored)
SUPABASE_URL=https://...
SUPABASE_ANON_KEY=...
SUPABASE_SERVICE_ROLE_KEY=... # Server-side only!
```

**Validation:**
```typescript
const requiredEnvVars = [
  'SUPABASE_URL',
  'SUPABASE_ANON_KEY'
]

for (const envVar of requiredEnvVars) {
  if (!process.env[envVar]) {
    throw new Error(`Missing environment variable: ${envVar}`)
  }
}
```

### API Security

**Rate Limiting:**
```typescript
import { Ratelimit } from '@upstash/ratelimit'

const ratelimit = new Ratelimit({
  redis: redis,
  limiter: Ratelimit.slidingWindow(100, '1 m')
})

const { success } = await ratelimit.limit(userId)
if (!success) {
  return Response.json({ error: 'Rate limit exceeded' }, { status: 429 })
}
```

**CORS Configuration:**
```typescript
// next.config.ts
const nextConfig = {
  async headers() {
    return [
      {
        source: '/api/:path*',
        headers: [
          { key: 'Access-Control-Allow-Origin', value: 'https://skaly.app' },
          { key: 'Access-Control-Allow-Methods', value: 'GET,POST,PUT,DELETE' },
          { key: 'Access-Control-Allow-Headers', value: 'Authorization, Content-Type' }
        ]
      }
    ]
  }
}
```

### Logging & Monitoring

**Log Security Events:**
```typescript
// Security event types
enum SecurityEvent {
  LOGIN_SUCCESS = 'login_success',
  LOGIN_FAILURE = 'login_failure',
  PASSWORD_RESET = 'password_reset',
  ACCOUNT_DELETED = 'account_deleted',
  SUSPICIOUS_ACTIVITY = 'suspicious_activity'
}

function logSecurityEvent(event: SecurityEvent, userId?: string, metadata?: object) {
  console.log(JSON.stringify({
    type: 'security',
    event,
    userId: userId ? maskUserId(userId) : null,
    timestamp: new Date().toISOString(),
    metadata
  }))
}
```

**Monitoring:**
- Failed login attempts (> 5 in 5 min → alert)
- Unusual data access patterns
- API error rates
- Database query performance

---

## Dependency Security

### Automated Scanning

**GitHub Dependabot:**
- Auto-scan for vulnerable dependencies
- Auto-create PRs for updates

**npm audit:**
```bash
# Run before every deploy
npm audit
npm audit fix
```

### Regular Updates

**Update schedule:**
- Security patches: Immediately
- Minor updates: Weekly
- Major updates: Monthly (with testing)

---

## Incident Response

### Security Incident Plan

**1. Detection**
- Monitoring alerts
- User reports
- Security audit

**2. Containment**
- Identify affected systems
- Block attacker access
- Preserve evidence

**3. Investigation**
- Determine scope
- Identify root cause
- Document timeline

**4. Remediation**
- Fix vulnerability
- Restore services
- Deploy patches

**5. Communication**
- Notify affected users (within 72h for GDPR)
- Public disclosure if necessary
- Post-mortem report

**6. Prevention**
- Update security measures
- Improve monitoring
- Team training

### Breach Notification

**GDPR Requirement:**
- Notify authorities within 72 hours
- Notify users if high risk
- Document the breach

**Template Email:**
```
Subject: Important Security Notice - SKALY

Dear [User],

We are writing to inform you of a security incident that may have affected your account.

What happened:
[Brief description]

What information was involved:
[Specific data types]

What we're doing:
[Actions taken]

What you should do:
[Recommended actions]

Contact:
security@skaly.app

Sincerely,
The SKALY Team
```

---

## Compliance Checklist

### Pre-Launch

- [ ] HTTPS enabled (TLS 1.3)
- [ ] RLS enabled on all tables
- [ ] Password requirements enforced
- [ ] Input validation on all forms
- [ ] CSRF protection implemented
- [ ] Rate limiting configured
- [ ] Security headers set
- [ ] Environment variables secured
- [ ] Privacy policy published
- [ ] Terms of service published
- [ ] Cookie consent implemented
- [ ] Data export feature working
- [ ] Account deletion feature working
- [ ] Security monitoring setup

### Ongoing

- [ ] Weekly dependency updates
- [ ] Monthly security audits
- [ ] Quarterly penetration testing
- [ ] Annual privacy policy review
- [ ] Incident response drills

---

## Security Headers

```typescript
// next.config.ts
const securityHeaders = [
  {
    key: 'X-DNS-Prefetch-Control',
    value: 'on'
  },
  {
    key: 'Strict-Transport-Security',
    value: 'max-age=63072000; includeSubDomains; preload'
  },
  {
    key: 'X-Frame-Options',
    value: 'SAMEORIGIN'
  },
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff'
  },
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block'
  },
  {
    key: 'Referrer-Policy',
    value: 'strict-origin-when-cross-origin'
  },
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=()'
  }
]
```

---

**Last Updated**: 2025-12-25
**Security Contact**: security@skaly.app
