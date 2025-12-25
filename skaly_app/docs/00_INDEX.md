# SKALY - Documentation Index

> **Navigation guide for all project documentation**

## Quick Start

New to the project? Read in this order:
1. [LLM_CONTEXT.md](../LLM_CONTEXT.md) - Essential context for AI agents
2. [01_VISION_PRD.md](./01_VISION_PRD.md) - Understand what we're building
3. [03_DATA_MODEL.md](./03_DATA_MODEL.md) - Database structure
4. [05_UI_NAVIGATION.md](./05_UI_NAVIGATION.md) - UI/UX overview

## Documentation Structure

### 📋 [01_VISION_PRD.md](./01_VISION_PRD.md)
**Vision & Product Requirements**
- Product vision and mission
- Target audience
- Problem statement
- Core features and differentiators
- Success metrics
- Business model

**When to read**: Starting a new feature, understanding product direction

---

### 👤 [02_USER_FLOWS.md](./02_USER_FLOWS.md)
**User Flows & User Stories**
- Key user personas
- User journey maps
- Feature-specific user flows
- Edge cases and error states
- Onboarding flow

**When to read**: Implementing user-facing features, UX work

---

### 🗄️ [03_DATA_MODEL.md](./03_DATA_MODEL.md)
**Database Schema & Data Model**
- Complete database schema (Supabase/PostgreSQL)
- Table relationships and foreign keys
- Row Level Security (RLS) policies
- Indexes and performance optimization
- Data migrations strategy

**When to read**: Database changes, API development, data queries

---

### 🧮 [04_CALCULATIONS_RULES.md](./04_CALCULATIONS_RULES.md)
**Business Logic & Calculation Rules**
- Investment capacity formula
- Net profit calculations
- Tax provisions (URSSAF, income tax)
- Net worth calculations
- Budget tracking logic
- Multi-business aggregation rules

**When to read**: Implementing financial calculations, business logic

---

### 🎨 [05_UI_NAVIGATION.md](./05_UI_NAVIGATION.md)
**UI/UX Specifications & Navigation**
- Page layouts and navigation structure
- Component library and design system
- Responsive design breakpoints
- Accessibility requirements
- Loading states and error handling

**When to read**: UI implementation, component development

---

### 🔌 [06_API_CONTRACTS.md](./06_API_CONTRACTS.md)
**API Endpoints & Contracts**
- REST API routes
- Request/response schemas
- Authentication requirements
- Error codes and handling
- Rate limiting

**When to read**: API development, frontend integration

---

### 🔒 [07_SECURITY_PRIVACY.md](./07_SECURITY_PRIVACY.md)
**Security & Privacy Guidelines**
- Authentication and authorization
- Data encryption
- RLS policies
- GDPR compliance
- PII handling
- Security best practices

**When to read**: Security review, handling sensitive data

---

### 🗺️ [08_ROADMAP.md](./08_ROADMAP.md)
**Development Roadmap**
- MVP features
- Phase 1, 2, 3 features
- Technical debt priorities
- Future enhancements
- Timeline estimates

**When to read**: Sprint planning, prioritization discussions

---

### 📖 [09_GLOSSARY.md](./09_GLOSSARY.md)
**Terms & Definitions**
- Business terminology
- French financial terms
- Technical jargon
- Acronyms

**When to read**: Clarifying terminology, onboarding

---

## Documentation Maintenance

### When to Update Documentation

- **Before coding**: Update specs if requirements change
- **During coding**: Add notes about implementation decisions
- **After coding**: Document what was actually built
- **Bug fixes**: Note edge cases discovered

### How to Update

1. Find the relevant document from the index above
2. Make changes inline, maintaining structure
3. Update "Last Updated" date at bottom of document
4. If major changes, add a changelog entry

### Documentation Standards

- Use markdown for all documentation
- Keep documents focused (one topic per file)
- Use code examples where helpful
- Include diagrams for complex flows (Mermaid syntax)
- Link between related documents

## Quick Reference by Task

| Task | Read These Docs |
|------|----------------|
| Adding a new page | 05_UI_NAVIGATION.md, 02_USER_FLOWS.md |
| Database change | 03_DATA_MODEL.md, 07_SECURITY_PRIVACY.md |
| New API endpoint | 06_API_CONTRACTS.md, 03_DATA_MODEL.md |
| Financial calculation | 04_CALCULATIONS_RULES.md |
| New business feature | 01_VISION_PRD.md, 04_CALCULATIONS_RULES.md |
| User flow change | 02_USER_FLOWS.md, 05_UI_NAVIGATION.md |
| Security review | 07_SECURITY_PRIVACY.md, 06_API_CONTRACTS.md |

## External Resources

- [Next.js 16 Documentation](https://nextjs.org/docs)
- [React 19 Documentation](https://react.dev)
- [Supabase Documentation](https://supabase.com/docs)
- [Tailwind CSS 4 Documentation](https://tailwindcss.com/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

## Contributing to Documentation

Good documentation:
- Is clear and concise
- Includes examples
- Stays up to date with code
- Answers "why" not just "what"
- Uses consistent terminology (see glossary)

---

**Last Updated**: 2025-12-25
**Maintained by**: Development Team
