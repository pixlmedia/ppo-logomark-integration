# PPO LogoMark Integration - Session Status

**Current Phase:** Phase 1 - Foundation (Weeks 1-2)  
**Last Updated:** 2026-01-03 (Session 2)  
**Updated By:** Claude Code (Dev Lead)  
**Next Session:** TBD (waiting for LogoMark field specification)

---

## Current State Summary

**Project Status:** Pre-Implementation (Ready to Begin Phase 1 on Field Spec Arrival)  
**Blocker:** Awaiting LogoMark field specification Excel file (promised by Michael end of week)

**What's Ready:**
- ✅ GitHub repository structure created
- ✅ Core documentation complete (SYSTEM_CHARTER, PROJECT_BRIEFING, ARCHITECTURE)
- ✅ LogoMark API key received: `BA0F7D3C-6A14-47D0-AFAA-19067BDB3224`
- ✅ Claude in Chrome extension installed and operational
- ✅ Multi-instance coordination framework established
- ✅ **Claude Code installed in VS Code and operational**
- ✅ **Tech stack decided: Node.js for SOAP client**
- ✅ **AI provider decided: Claude API for description enhancement**
- ✅ **Hosting approach: GitHub Actions for scheduled syncs (final decision deferred)**

**What's Blocked:**
- 🚫 SOAP client implementation (need field spec to finalize request/response mapping)
- 🚫 Easify option schema design (need to know if LogoMark provides variant data)
- 🚫 Data transformation logic (need actual data structure from field spec)

---

## Session History

### Session 2: 2026-01-03 (Tech Stack Decisions & Setup Completion)
**Participants:** Chris, Claude Code (Dev Lead)

**Completed:**
- ✅ Claude Code installed in VS Code and confirmed operational
- ✅ Updated SESSION_STATUS.md with current state

**Decisions Made:**
- **SOAP Client Tech Stack:** Node.js (confirmed by Chris)
- **AI Provider:** Claude API for description enhancement (confirmed by Chris)
- **Hosting Strategy:** GitHub Actions for scheduled syncs (final implementation details deferred to later)

**Open Questions:**
1. **Tablepress automation?** (Can we generate tables via API or must we use Shopify metafields + frontend rendering?)
2. **Easify option creation?** (Auto-generate if data available, or manual templates per category?)
3. **Sync Frequency:** How often to poll LogoMark? (Daily? Hourly? On-demand?)

**Next Session Goals:**
- Wait for LogoMark field specification from Michael (expected end of week)
- Once field spec received: Begin Phase 1 SOAP client implementation
- Set up Node.js project structure with SOAP library

---

### Session 1: 2026-01-03 (Initial Planning)
**Participants:** Chris, Claude.ai (CTO), Claude in Chrome (setup only)

**Completed:**
- Created GitHub repo `ppo-logomark-integration`
- Wrote PROJECT_BRIEFING.md (business requirements, scope, roadmap)
- Wrote ARCHITECTURE.md (technical design, API endpoints, data flows)
- Wrote SYSTEM_CHARTER.md (governance, session discipline, AI content contract)
- Established multi-instance coordination framework
- Documented LogoMark API endpoints and authentication

**Decisions Made:**
- Use Node.js for SOAP client (pending Chris confirmation)
- GitHub Actions for scheduled product syncs (Make.com for order tracking)
- Maintain conceptual layer separation (Connector vs Domain Logic) for future UiPath/mini-PLTR compatibility
- AI description enhancement via Claude API (contract defined in charter)
- Strict "no memory outside artifacts" rule for all Claude instances

**Open Questions:**
1. **Node.js vs PHP for SOAP client?** (Chris has experience with both via ChatGPT assistance)
2. **Where to host the integration?** (Shopify custom app? External server? GitHub Actions only?)
3. **AI API choice for descriptions?** (Claude API vs OpenAI? Cost/latency trade-offs?)
4. **Tablepress automation?** (Can we generate tables via API or must we use Shopify metafields + frontend rendering?)
5. **Easify option creation?** (Auto-generate if data available, or manual templates per category?)

**Next Session Goals:**
- Chris installs Claude Code in VS Code
- Chris confirms SOAP client tech stack preference (Node.js/PHP)
- Wait for LogoMark field spec to arrive
- Once field spec received: Begin SOAP client implementation (Phase 1)

---

## Phase 1 Checklist (Foundation)

**Target:** Weeks 1-2 (Jan 2026)

- [ ] **Receive LogoMark field specification** (blocker - waiting on Michael)
- [x] **Choose SOAP client tech stack** ✅ **Decision: Node.js**
- [ ] **Set up development environment**
  - [x] Claude Code installed in VS Code ✅
  - [ ] SOAP library dependency installed
  - [ ] Test credentials against LogoMark sandbox (if available)
- [ ] **Build SOAP client foundation**
  - [ ] Authentication module (API key header)
  - [ ] Base request/response handling
  - [ ] Error handling & retry logic (3 attempts, exponential backoff)
  - [ ] Logging infrastructure (correlation IDs, redaction rules)
- [ ] **Implement Product Data 2.0 integration**
  - [ ] Parse product metadata (name, SKU, description, MOQ)
  - [ ] SKU transformation (ASCII shift: PKACBB → PPDBCC)
  - [ ] Basic validation (SKU format, MOQ >= 10)
- [ ] **Test basic product import flow**
  - [ ] Fetch 1 sample product from LogoMark
  - [ ] Transform data
  - [ ] Log output (don't write to Shopify yet)
  - [ ] Verify SKU shift logic
  - [ ] Verify description enhancement works

**Phase 1 Success Criteria:**
- Can successfully authenticate with LogoMark API
- Can fetch product data from Product Data 2.0 endpoint
- SKU transformation produces correct output
- Logging captures all requests/responses with correlation IDs

---

## Known Technical Decisions

### Confirmed
- **API Authentication:** API key in SOAP header (key: `BA0F7D3C-6A14-47D0-AFAA-19067BDB3224`)
- **SKU Format:** `PPO-[shifted LogoMark SKU]` (ASCII +1 shift with wrap)
- **Description Enhancement:** AI via Claude API (2-3 sentences, PPO brand voice) ✅
- **SOAP Client Tech:** Node.js ✅
- **AI API Provider:** Claude API ✅
- **Hosting Strategy:** GitHub Actions for scheduled syncs (implementation details TBD) ✅
- **No Sample Pricing:** LogoMark products are bulk-only (MOQ enforced)
- **Scope:** PPO store only (`daznia-z0.myshopify.com`)

### Pending Chris Approval
- **Sync Frequency:** How often to poll LogoMark? (Daily? Hourly? On-demand?)

### Deferred to Later Phases
- **Tablepress Automation:** Phase 2
- **Easify Option Schema:** Phase 2 (depends on field spec)
- **Media Content 1.1 Integration:** Phase 3
- **Inventory Sync:** Phase 4
- **Order Tracking:** Phase 4

---

## Current Blockers

### 🚨 Critical Path Blocker
**LogoMark Field Specification Missing**
- **Impact:** Cannot finalize SOAP request/response mapping
- **ETA:** End of this week (per Michael)
- **Workaround:** None - must wait
- **Preparation:** Review PromoStandards schema docs to anticipate field structure

---

## Integration Points to Monitor

### Shopify
- Store: `daznia-z0.myshopify.com`
- Apps: Tablepress (pricing tables), Easify (product options)
- Risk: Easify option complexity (waterfall dependencies)
- Next: Set up custom app credentials for REST API access

### Make.com
- Current: Salesforce integration for PK orders
- Plan: Extend for LogoMark order tracking (Phase 4)
- Risk: None currently
- Next: No action until Phase 4

### LogoMark API
- Endpoints: 6 SOAP services (see ARCHITECTURE.md)
- Auth: API key ready
- Risk: Unknown field structure until spec arrives
- Next: Test authentication once spec received

---

## Technical Debt & Future Considerations

### Mini-PLTR Readiness
- **Current:** System designed with conceptual layer separation (Connector vs Domain Logic)
- **Action Needed:** None yet - maintain separation as we build
- **Review Point:** After Phase 2 (when domain logic is more complex)

### UiPath Compatibility
- **Current:** PromoStandards compliance ensures normalized data
- **Action Needed:** Use correlation IDs in all logs (implement in Phase 1)
- **Review Point:** Before mini-PLTR project begins

### Permission Model
- **Current:** Owner-level access assumed
- **Action Needed:** Document all API scopes used
- **Review Point:** Before CSR/chatbot integration (future)

---

## Communication Preferences

**Chris's Preferred Style (from initial session):**
- Concise and directive when possible (executive summary style)
- Detailed technical explanations when architecture decisions are at stake
- Avoid over-explanation of basics (Chris has 20 years e-commerce experience)
- Flag risks/trade-offs clearly

**Session Cadence:**
- Ad-hoc as Chris has time
- No regular schedule yet
- Expect sessions to increase in frequency once field spec arrives and Phase 1 coding begins

---

## Notes for All Claude Instances

### For Claude.ai (CTO)
- You are the "decider" when Code/Chrome instances need direction
- Always review this status doc before providing architectural guidance
- Escalate to Chris if decision is outside approved architecture

### For Claude Code (Dev Lead)
- **Start every session:** Read this file + SYSTEM_CHARTER.md
- **End every session:** Update this file with progress (use template above)
- Your commits should reference task checklist items
- If you encounter an undocumented decision point, flag it here in "Open Questions"

### For Claude in Chrome (Ops Lead)
- Check this file before making changes in Shopify admin
- If you're about to modify metafields/products, confirm it aligns with current phase
- Document any manual workarounds you discover (e.g., Easify quirks)

---

**Last Session:** 2026-01-03 Session 2 (Tech Stack Decisions & Setup Completion)  
**Next Session:** TBD (waiting for LogoMark field spec from Michael)  
**Status:** Pre-Implementation (Ready for Phase 1 once field spec arrives)
