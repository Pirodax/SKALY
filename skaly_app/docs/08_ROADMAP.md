# SKALY - Development Roadmap

## Current Status

**Version**: 0.1.0 (Pre-MVP)
**Phase**: Initial Development
**Target MVP Launch**: Q2 2026

---

## MVP - Phase 1 (Foundation)

**Target**: End of Q1 2026
**Goal**: Core functionality for single freelancer

### Sprint 1: Setup & Authentication (2 weeks)
- [x] Project initialization (Next.js 16 + Supabase)
- [x] Documentation structure
- [ ] Supabase database schema implementation
- [ ] Supabase Auth integration
- [ ] RLS policies setup
- [ ] User profile management
- [ ] Basic UI layout with Tailwind CSS

### Sprint 2: Personal Finance (3 weeks)
- [ ] Personal transaction CRUD
- [ ] Transaction categorization
- [ ] Budget creation and tracking
- [ ] Budget alerts
- [ ] Monthly summary view
- [ ] Personal dashboard cards

### Sprint 3: Single Business Tracking (3 weeks)
- [ ] Business creation
- [ ] Business transaction CRUD
- [ ] URSSAF calculation logic
- [ ] Income tax provision calculation
- [ ] Business dashboard
- [ ] Business metrics calculation

### Sprint 4: Investment Capacity (2 weeks)
- [ ] Investment capacity calculator
- [ ] Capacity breakdown view
- [ ] Investment allocation setup
- [ ] Allocation distribution display
- [ ] Real-time capacity updates

### Sprint 5: Basic Net Worth (2 weeks)
- [ ] Asset management (liquid savings, investments)
- [ ] Liability management (loans, debts)
- [ ] Net worth calculation
- [ ] Net worth evolution chart
- [ ] Simple projections

### Sprint 6: Overview Dashboard (2 weeks)
- [ ] Dashboard summary API
- [ ] Summary cards integration
- [ ] Recent transactions feed
- [ ] Alert system
- [ ] Navigation polish

### Sprint 7: Testing & Bug Fixes (2 weeks)
- [ ] Unit tests for calculation logic
- [ ] Integration tests for API routes
- [ ] E2E tests for critical flows
- [ ] Bug fixes
- [ ] Performance optimization
- [ ] Security audit

### Sprint 8: Beta Launch Prep (1 week)
- [ ] Documentation review
- [ ] Privacy policy finalization
- [ ] Terms of service
- [ ] Onboarding flow
- [ ] Demo data seeding
- [ ] Beta user recruitment

**MVP Deliverables:**
- ✅ User authentication
- ✅ Personal finance tracking
- ✅ Single business tracking
- ✅ Investment capacity calculator
- ✅ Basic net worth tracking
- ✅ Dashboard with key metrics
- ✅ Budget management
- ✅ Mobile responsive

---

## Phase 2 - Growth Features

**Target**: Q3-Q4 2026
**Goal**: Multi-business support and advanced features

### 2.1 Multi-Business Support (4 weeks)
- [ ] Multi-business architecture
- [ ] Business switching UI
- [ ] Consolidated business view
- [ ] Business comparison table
- [ ] Cross-business analytics
- [ ] Business profitability insights

### 2.2 Advanced Investment Features (3 weeks)
- [ ] Investment goals with deadlines
- [ ] Goal progress tracking
- [ ] Savings simulation tool
- [ ] Investment performance tracking
- [ ] Automatic goal recommendations
- [ ] Custom investment categories

### 2.3 Enhanced Net Worth (3 weeks)
- [ ] Real estate tracking
- [ ] Vehicle tracking with depreciation
- [ ] Other assets (valuables, business equity)
- [ ] Detailed liability management
- [ ] Amortization schedules
- [ ] Equity progression charts

### 2.4 Subscriptions Management (2 weeks)
- [ ] Subscription CRUD
- [ ] Automatic subscription detection
- [ ] Subscription cost analysis
- [ ] Duplicate subscription warnings
- [ ] Cancellation reminders
- [ ] Subscription optimization tips

### 2.5 Recurring Transactions (2 weeks)
- [ ] Recurring pattern detection
- [ ] Auto-categorization suggestions
- [ ] Recurring transaction rules
- [ ] Bulk categorization
- [ ] Smart transaction matching

### 2.6 Advanced Analytics (4 weeks)
- [ ] Trend analysis
- [ ] Month-over-month comparisons
- [ ] Category spending trends
- [ ] Business profitability trends
- [ ] Custom date range reports
- [ ] Export to CSV/PDF

**Phase 2 Deliverables:**
- ✅ Multi-business full support
- ✅ Advanced investment tracking
- ✅ Comprehensive net worth
- ✅ Smart subscriptions
- ✅ Recurring transaction automation
- ✅ Rich analytics

---

## Phase 3 - Automation & Integrations

**Target**: Q1-Q2 2027
**Goal**: Reduce manual work, increase accuracy

### 3.1 Bank Connection (6 weeks)
- [ ] Research bank API providers (Bridge, Tink, Plaid)
- [ ] Provider integration
- [ ] Bank account connection flow
- [ ] Transaction synchronization
- [ ] Auto-categorization (ML-based)
- [ ] Duplicate detection
- [ ] Balance reconciliation

### 3.2 Tax Optimization (4 weeks)
- [ ] Legal status comparison tool
- [ ] Tax simulation (ME vs SASU vs SARL)
- [ ] Optimal salary calculation (SASU)
- [ ] Dividend vs salary optimization
- [ ] Tax deduction tracking
- [ ] Tax calendar & reminders

### 3.3 Accounting Integration (4 weeks)
- [ ] Export to accounting software
- [ ] Invoice integration (if applicable)
- [ ] Accountant collaboration mode
- [ ] Read-only sharing
- [ ] Custom report generation
- [ ] Audit trail

### 3.4 AI-Powered Insights (5 weeks)
- [ ] Spending pattern analysis
- [ ] Revenue prediction (ML model)
- [ ] Budget recommendations
- [ ] Investment optimization suggestions
- [ ] Business health scoring
- [ ] Anomaly detection

### 3.5 Mobile App (8 weeks)
- [ ] React Native app setup
- [ ] Authentication flow
- [ ] Dashboard mobile UI
- [ ] Quick transaction entry
- [ ] Push notifications
- [ ] Offline mode
- [ ] App store submission

**Phase 3 Deliverables:**
- ✅ Bank auto-sync
- ✅ Tax optimization tools
- ✅ Accounting integrations
- ✅ AI insights
- ✅ Native mobile apps

---

## Phase 4 - Scale & Community

**Target**: Q3-Q4 2027
**Goal**: Build community, expand reach

### 4.1 Freemium Launch (3 weeks)
- [ ] Pricing tiers implementation
- [ ] Feature gating
- [ ] Stripe payment integration
- [ ] Subscription management
- [ ] Upgrade/downgrade flows
- [ ] Billing portal

### 4.2 Templates & Best Practices (4 weeks)
- [ ] Financial templates library
- [ ] Budget templates by persona
- [ ] Business setup guides
- [ ] Tax optimization guides
- [ ] Video tutorials
- [ ] Knowledge base

### 4.3 Community Features (5 weeks)
- [ ] User forum/community
- [ ] Anonymous benchmarking
- [ ] Success stories
- [ ] Expert Q&A
- [ ] Freelancer directory (opt-in)
- [ ] Referral program

### 4.4 Advanced Projections (4 weeks)
- [ ] Monte Carlo simulations
- [ ] Scenario planning
- [ ] Retirement planning
- [ ] Business growth modeling
- [ ] What-if analysis
- [ ] Financial independence calculator

### 4.5 Marketplace (6 weeks)
- [ ] Partner integrations (insurance, banks)
- [ ] Product recommendations
- [ ] Affiliate partnerships
- [ ] Business tools marketplace
- [ ] Revenue sharing

**Phase 4 Deliverables:**
- ✅ Monetization live
- ✅ Content library
- ✅ Active community
- ✅ Advanced planning tools
- ✅ Partner ecosystem

---

## Technical Debt & Maintenance

### Ongoing Tasks

**Weekly:**
- Dependency updates
- Security patches
- Bug triage
- Performance monitoring

**Monthly:**
- Code review & refactoring
- Database optimization
- Load testing
- Security audit

**Quarterly:**
- Architecture review
- UX research
- Feature usage analysis
- Accessibility audit

### Infrastructure Improvements

**Q2 2026:**
- [ ] CDN setup
- [ ] Database indexing optimization
- [ ] Caching strategy (Redis)
- [ ] Monitoring & alerting (Sentry)

**Q3 2026:**
- [ ] Horizontal scaling setup
- [ ] Disaster recovery plan
- [ ] Automated backups
- [ ] Performance profiling

**Q4 2026:**
- [ ] Multi-region deployment
- [ ] Advanced caching
- [ ] Database sharding (if needed)
- [ ] Service-level objectives (SLOs)

---

## Feature Requests Backlog

### High Priority
- [ ] Multi-currency support
- [ ] Team/agency mode (multiple users per business)
- [ ] Invoice management
- [ ] Expense scanning (OCR)
- [ ] Bank reconciliation tools

### Medium Priority
- [ ] Time tracking integration
- [ ] Project profitability
- [ ] Client portal
- [ ] Automated financial reports
- [ ] Tax document upload

### Low Priority (Future)
- [ ] Cryptocurrency portfolio tracking
- [ ] Stock trading integration
- [ ] Crowdfunding campaign tracking
- [ ] Loan comparison tool
- [ ] Insurance policy management

---

## Success Metrics

### MVP Success Criteria
- 1000+ registered users
- 100+ active weekly users
- 60% D7 retention
- NPS > 40
- < 5 critical bugs

### Phase 2 Success Criteria
- 5000+ registered users
- 500+ active weekly users
- 10% conversion to Pro tier
- 70% D7 retention
- NPS > 50

### Phase 3 Success Criteria
- 20000+ registered users
- 3000+ active weekly users
- 15% conversion rate
- 50% M1 retention
- Profitability reached

### Phase 4 Success Criteria
- 100000+ registered users
- 15000+ paying customers
- Sustainable MRR growth
- Active community (1000+ members)
- Partner ecosystem launched

---

## Risks & Mitigation

### Technical Risks

**Risk**: Supabase scaling issues
**Mitigation**: Monitor usage, plan migration to self-hosted if needed

**Risk**: Bank API reliability
**Mitigation**: Multi-provider strategy, fallback to manual entry

**Risk**: Security breach
**Mitigation**: Regular audits, bug bounty, incident response plan

### Business Risks

**Risk**: Low user adoption
**Mitigation**: User research, iterative MVP, early beta feedback

**Risk**: Competition
**Mitigation**: Focus on niche (freelancers), differentiation, community

**Risk**: Regulatory changes (GDPR, financial regulations)
**Mitigation**: Legal counsel, compliance monitoring, adaptable architecture

---

## Decision Log

### ADR-001: Next.js vs Remix
**Date**: 2025-12-25
**Decision**: Next.js 16
**Reasoning**:
- Larger ecosystem
- Better Vercel integration
- More familiar to team
- Server Components maturity

### ADR-002: Supabase vs Firebase
**Date**: 2025-12-25
**Decision**: Supabase
**Reasoning**:
- PostgreSQL (more powerful than Firestore)
- Better RLS support
- Open source
- EU hosting option (GDPR)

### ADR-003: Monolith vs Microservices
**Date**: 2025-12-25
**Decision**: Monolith (for MVP)
**Reasoning**:
- Faster development
- Simpler deployment
- Can refactor later if needed
- Appropriate for current scale

---

**Last Updated**: 2025-12-25
**Next Review**: 2026-01-15
