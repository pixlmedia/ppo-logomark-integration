# PPO LogoMark Integration - System Charter

**Version:** 1.0  
**Last Updated:** 2026-01-03  
**Authority:** Chris (Business Owner/Architect)

---

## What This System Is

A **PromoStandards-compliant SOAP integration** that imports LogoMark products into PPO's Shopify store with:
- Automated SKU transformation (ASCII shift algorithm)
- AI-enhanced product descriptions (Claude API)
- Dynamic Tablepress pricing tables (quantity × option matrix)
- Easify waterfall options (size-dependent customization)
- Real-time inventory synchronization
- Order status tracking integration

**Scope:** PPO store only (`daznia-z0.myshopify.com`) - no changes to PK/PromotionalKeychains.biz

---

## What This System Is NOT

- ❌ Not a custom/proprietary API extension (strict PromoStandards compliance)
- ❌ Not a PK store integration (separate codebase, separate business logic)
- ❌ Not a replacement for Make.com Salesforce workflows (augments existing automation)
- ❌ Not a UiPath normalization layer (though designed to be UiPath-compatible for future mini-PLTR)
- ❌ Not storing session memory in AI chat history (all state lives in GitHub artifacts)

---

## System Boundaries & Future Compatibility

### Current Architecture
```
LogoMark SOAP APIs → Node.js Connector → Domain Logic → Shopify REST API
                                ↓
                        AI Enhancement (Claude)
                        SKU Transformation
                        Pricing Logic
```

### Conceptual Layer Separation (for UiPath/mini-PLTR readiness)
1. **Connector Layer** (pure SOAP ↔ JSON normalization)
   - API communication
   - Authentication
   - Error handling/retries
   - *Future: replaceable by UiPath connectors*

2. **Domain Logic Layer** (business rules & transformations)
   - SKU shifting algorithm
   - Pricing table generation
   - Easify option mapping
   - AI content enhancement
   - *Future: consumed by mini-PLTR intelligence layer*

3. **Integration Layer** (Shopify-specific writes)
   - Product creation
   - Metafield storage
   - Inventory updates
   - *Future: permission-aware (CSR/chatbot access controls)*

**Important:** Code does NOT need to be physically separated yet. This is a *conceptual boundary* to prevent future refactoring pain.

---

## AI Content Contract

### Description Enhancement (Claude API)

**Input Constraints:**
- Source: LogoMark `Product Data 2.0` description field
- Max input length: 500 characters
- Must contain: product category, base material, customization method

**Output Requirements:**
- Length: 2-3 sentences (40-80 words)
- Tone: Contemporary, professional, energetic (PPO brand voice - NOT editorial like PK)
- Structure: [Product benefit] + [Key feature] + [Use case/audience]
- Example: "Premium custom belt buckles with full-color engraving. Make a statement with personalized buckles that reflect your unique style. Perfect for corporate gifts or branded merchandise."

**Forbidden Content:**
- ❌ Pricing claims ("affordable", "best value")
- ❌ Compliance/certification claims (unless in source data)
- ❌ Guarantees/promises ("guaranteed satisfaction")
- ❌ Origin/manufacturing location (unless in source data)
- ❌ Comparative claims ("better than", "superior to")
- ❌ Superlatives without evidence ("best", "most popular")

**Quality Checks:**
- Description must pass Shopify's spam filter
- No repeated phrases from LogoMark source
- No AI-detection red flags (varies per model, check monthly)

---

## Permissions & Access Control

### Current State: Owner-Level Access
This system currently assumes **full admin permissions** for:
- Shopify store (product creation, metafield writes, inventory updates)
- LogoMark API (all 6 endpoints)
- GitHub repository (read/write on all branches)
- Make.com scenarios (webhook triggers)

### Future State: Layered Permissions (mini-PLTR readiness)
**Design Note:** When this system becomes part of the broader mini-PLTR:
- CSR access: Read-only product data, order status queries
- Chatbot access: Customer-facing inventory checks, order tracking
- Admin access: Full CRUD on products, pricing, options

**Action Required Before Chatbot/CSR Integration:**
1. Audit all API calls for permission scopes
2. Add role-based access control (RBAC) layer
3. Implement audit logging for all writes
4. Define data redaction rules (API keys, customer PII)

---

## Session Discipline - How Claude Instances Work

### Instance Roles

**Claude.ai (CTO - Strategic Oversight)**
- Architecture decisions
- Cross-system integration design
- Session planning & status review
- Final approval on major structural changes
- Coordinating handoffs between Code/Chrome instances

**Claude Code (Dev Lead - Codebase Work)**
- File operations (read/write MD docs, code files)
- Git operations (commits, branches, PRs)
- Code generation & refactoring
- Test execution
- Status document updates

**Claude in Chrome (Ops Lead - Web Admin)**
- Shopify admin tasks (metafields, products, settings)
- Make.com scenario building/testing
- Browser-based verification
- Web UI interactions (logged-in sessions)

---

### Session Start Protocol

**Every session begins with:**

1. **Claude Code reads:**
   - `SESSION_STATUS.md` (catch up on progress)
   - `IMPLEMENTATION_CHECKLIST.md` (current task list)
   - Relevant architecture docs for today's work

2. **Claude.ai reviews:**
   - Session objectives from Chris
   - Any blockers flagged in status doc
   - Coordination needs between instances

3. **Claude in Chrome checks:**
   - Shopify admin state (if product work planned)
   - Make.com scenario status (if order flow work planned)

**No session memory carries over** - all context must be explicitly loaded from files.

---

### Session End Protocol

**Claude Code must update before session close:**

```markdown
## SESSION_STATUS.md Update Template

### Session Date: [YYYY-MM-DD]

**Completed:**
- [Concrete deliverable 1 with file/commit reference]
- [Concrete deliverable 2]

**In Progress:**
- [Task name] - [blocker/next step]

**Decisions Made:**
- [Architecture decision 1]
- [Trade-off accepted/deferred]

**Open Questions:**
- [Question for Chris]
- [Technical uncertainty to resolve]

**Next Session Goals:**
- [Clear, actionable task 1]
- [Clear, actionable task 2]
```

**Commit hygiene:**
- Descriptive commit messages (not "update files")
- Reference issue/task numbers if tracking in GitHub
- Tag commits with session date

---

## Data Integrity Rules

### Logging & Observability

**Log Retention:**
- SOAP requests/responses: 90 days
- Product import jobs: 1 year
- Error logs: 2 years
- Audit logs (future): 7 years (compliance requirement)

**Redaction Rules:**
- LogoMark API key: redact from all logs
- Customer PII: redact from debug logs
- Pricing data: redact from public error messages

**Correlation IDs:**
Every job/run must generate:
- Job ID (UUID)
- SKU being processed
- Timestamp (ISO 8601)
- LogoMark product ID

**Log Locations:**
- Development: Console output + local file (`logs/dev-YYYY-MM-DD.log`)
- Production: Structured logging to [TBD: Shopify Flow logs? External service?]

---

## Change Management

### Who May Change What

**Chris (Owner):**
- Change system charter (this document)
- Approve architecture revisions
- Override AI decisions
- Grant/revoke Claude instance permissions

**Claude.ai (CTO):**
- Propose architecture changes (requires Chris approval)
- Coordinate multi-instance work
- Refine implementation roadmap

**Claude Code (Dev Lead):**
- Write/refactor code (within approved architecture)
- Update implementation checklist
- Update session status
- Commit to `main` branch (for now - may add PR requirement later)

**Claude in Chrome (Ops Lead):**
- Modify Shopify admin (products, metafields, settings)
- Build/edit Make.com scenarios
- Verify production deployments

### Escalation Path
1. Claude instance encounters decision point outside scope → Flag in status doc
2. Claude.ai reviews and either: (a) approves within architecture bounds, or (b) escalates to Chris
3. Chris decides → Update charter/architecture docs if precedent-setting

---

## No Memory Outside Artifacts Rule

**Critical Principle:** AI instances have **zero persistent memory** between sessions.

**All state must live in:**
- GitHub files (markdown, code, config)
- Shopify data (products, metafields)
- External systems (Make.com, LogoMark API)

**Forbidden:**
- "I remember we discussed..." (unless referring to a file)
- "Last time you said..." (unless in SESSION_STATUS.md)
- Assuming context from previous chat history

**Correct Pattern:**
- "According to SESSION_STATUS.md, the last session completed X"
- "ARCHITECTURE.md specifies Y"
- "Checking current Shopify metafield state..."

---

## Integration with Future Mini-PLTR

This system is designed to be **consumed** by the future mini-PLTR:

**Data Normalization (UiPath Layer):**
- Connector layer outputs normalized JSON (already PromoStandards-compliant)
- Domain logic layer is stateless (can be called by UiPath orchestrator)
- Logs use correlation IDs (compatible with UiPath audit trails)

**Mini-PLTR Intelligence Layer Will:**
- Query normalized product data across all suppliers (not just LogoMark)
- Apply business rules on top of domain logic (pricing strategies, inventory allocation)
- Enforce permissions for CSR/chatbot access
- Generate insights ("LogoMark stock low on product X, reorder?")

**This System Provides:**
- One supplier's data feed (LogoMark)
- Proven transformation patterns (SKU shifting, AI enhancement)
- Reference architecture for additional supplier integrations

**Deferred Until Mini-PLTR:**
- Cross-supplier analytics
- Dynamic pricing rules (beyond quantity breaks)
- Predictive inventory management
- CSR/chatbot natural language queries

---

## Success Criteria Reminder

From PROJECT_BRIEFING.md - these are the immutable goals:

- ✅ All LogoMark products available on PPO with accurate pricing
- ✅ Tablepress tables auto-generated from LogoMark data
- ✅ Easify options properly configured (waterfall dependent options work)
- ✅ Product images display correctly
- ✅ Inventory levels sync from LogoMark
- ✅ Order status visible in customer accounts
- ✅ AI-enhanced descriptions meet PPO brand voice
- ✅ **No manual data entry required** (except Easify setup if unavoidable)

---

## Document Hierarchy

**Read in this order when resuming work:**

1. `SYSTEM_CHARTER.md` (this file) - governance & discipline
2. `SESSION_STATUS.md` - current state & next tasks
3. `PROJECT_BRIEFING.md` - business context & scope
4. `ARCHITECTURE.md` - technical design
5. Task-specific docs (`DATA_MAPPING.md`, `SOAP_CLIENT_SETUP.md`, etc.)

---

**Charter Authority:** Chris  
**Last Reviewed:** 2026-01-03  
**Next Review:** After Phase 1 completion (or major architecture change)
