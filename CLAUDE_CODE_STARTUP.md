# Claude Code Startup Protocol

**Purpose:** This file tells Claude Code exactly what to do when a session starts.  
**Owner:** This is read by Claude Code (Dev Lead instance) - do not modify without CTO approval.

---

## Session Startup Checklist (Run This Every Time)

### 1. Load Context (in this order)
```bash
# Read these files in sequence:
cat SYSTEM_CHARTER.md          # Governance & rules
cat SESSION_STATUS.md          # Current state & blockers
cat IMPLEMENTATION_CHECKLIST.md # Today's task list
```

**Expected Time:** 2-3 minutes of reading time

**What to Look For:**
- Current phase (SESSION_STATUS.md)
- Any blockers flagged (SESSION_STATUS.md)
- Today's goals from Chris (will be in chat message)
- Last session's "Next Session Goals" (SESSION_STATUS.md)

---

### 2. Verify Repository State
```bash
# Check which branch you're on
git branch

# Check for uncommitted changes
git status

# Pull latest if working with others
git pull origin main
```

**Expected State:**
- On `main` branch (unless Chris says otherwise)
- No uncommitted changes (last session should have committed everything)
- Up to date with remote

**If Unexpected:**
- Uncommitted changes exist → Ask Chris if you should commit or stash
- Not on `main` → Ask Chris if intentional
- Behind remote → Pull and review changes

---

### 3. Confirm Today's Objective
Before starting work, ask Chris:

**Example prompt:**
> "I've loaded SESSION_STATUS.md - last session completed [X]. The next goals listed were [Y]. Is that still the plan for today, or do you want to focus on something else?"

**Why This Matters:**
- Chris may have received LogoMark field spec since last session
- Priorities may have shifted
- New blocker may have appeared

---

### 4. Check Dependencies (if coding today)
```bash
# For Node.js SOAP client (if that's the choice):
npm list    # Verify dependencies installed

# For PHP SOAP client (if that's the choice):
composer show   # Verify dependencies installed
```

**If Missing Dependencies:**
- Install per package.json or composer.json
- Document any new dependencies added

---

## Session End Protocol (Run Before Closing)

### 1. Update SESSION_STATUS.md

**Required Template:**
```markdown
### Session [N]: YYYY-MM-DD (Brief Title)
**Participants:** Chris, Claude Code

**Completed:**
- [Deliverable 1 with commit SHA or file reference]
- [Deliverable 2]

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

**Append to Session History section** (don't overwrite existing sessions)

---

### 2. Commit All Work
```bash
# Stage all changes
git add .

# Commit with descriptive message
git commit -m "Session [N] YYYY-MM-DD: [Brief summary of work]

- Completed: [deliverable 1]
- Completed: [deliverable 2]
- Updated: SESSION_STATUS.md

Refs: #[issue number if applicable]"

# Push to remote
git push origin main
```

**Commit Message Rules:**
- First line: Session identifier + brief summary (50 chars max)
- Blank line
- Bullet list of deliverables
- Reference SESSION_STATUS.md update
- Reference any GitHub issues

**Bad Commit Message:**
```
Updated files
```

**Good Commit Message:**
```
Session 2 2026-01-05: Implement SKU transformation module

- Created src/transformers/sku-shifter.js
- Added unit tests for ASCII shift logic
- Updated SESSION_STATUS.md with progress

Refs: Phase 1 checklist
```

---

### 3. Verify Clean State
```bash
# Should show "nothing to commit, working tree clean"
git status
```

**If Not Clean:**
- You missed something - add and commit it
- Or explicitly document why files are uncommitted (e.g., local config that shouldn't be in repo)

---

## Special Situations

### When Field Spec Arrives
**Trigger:** Chris says "LogoMark sent the field spec"

**Actions:**
1. Ask Chris to upload the Excel file to GitHub repo (or share screen capture if needed)
2. Create new file: `LOGOMARK_FIELD_SPEC.md` (parsed summary of Excel for easy reference)
3. Update `DATA_MAPPING.md` with actual field mappings (replace TBD sections)
4. Flag any unexpected fields or missing required fields in SESSION_STATUS.md

---

### When Major Architecture Change Proposed
**Trigger:** You or Chris suggests changing a core design decision

**Actions:**
1. **Do NOT implement immediately**
2. Document the proposal in SESSION_STATUS.md under "Open Questions"
3. Tag Claude.ai (CTO) for review: "This requires CTO review - proposed change: [description]"
4. Wait for Chris or Claude.ai approval before proceeding

**Examples of Major Changes:**
- Switching from Node.js to PHP
- Adding a new external service (beyond LogoMark/Shopify)
- Changing SKU transformation algorithm
- Modifying AI content contract

---

### When You Encounter an Undocumented Edge Case
**Trigger:** Something isn't covered in the docs (e.g., "What if LogoMark returns null for description?")

**Actions:**
1. Make a reasonable assumption (document it in code comments)
2. Add to SESSION_STATUS.md under "Decisions Made" with note: "Assumption made - needs Chris review"
3. Implement the code with clear TODO comment: `// TODO: Verify assumption - what if description is null?`
4. Continue working (don't block on every edge case)

---

## File Structure Expectations

**Your workspace:**
```
ppo-logomark-integration/
├── docs/
│   ├── SYSTEM_CHARTER.md         (read-only unless Chris approves changes)
│   ├── SESSION_STATUS.md          (you update at end of session)
│   ├── PROJECT_BRIEFING.md        (read-only)
│   ├── ARCHITECTURE.md            (read-only)
│   └── [other reference docs]
├── src/
│   ├── soap-client/               (your code goes here)
│   ├── transformers/              (SKU shift, pricing, etc.)
│   ├── ai-enhancement/            (description enhancement)
│   └── shopify-integration/       (REST API wrapper)
├── tests/
│   ├── unit/
│   └── integration/
├── config/
│   ├── credentials.example.json   (committed)
│   └── credentials.json           (gitignored - Chris provides)
└── logs/
    └── dev-YYYY-MM-DD.log         (gitignored)
```

**What You Create:**
- Code files in `src/`
- Test files in `tests/`
- Documentation updates in `docs/` (SESSION_STATUS.md primarily)

**What You Don't Touch Without Approval:**
- SYSTEM_CHARTER.md (Chris only)
- PROJECT_BRIEFING.md (frozen scope - changes need CTO review)
- ARCHITECTURE.md (CTO approval required for edits)

---

## Communication with Other Claude Instances

### With Claude.ai (CTO)
**When to Escalate:**
- Architecture decision needed
- Blocker preventing progress
- Trade-off analysis required (e.g., performance vs complexity)

**How to Escalate:**
- Update SESSION_STATUS.md with clear problem statement
- Tag in chat: "CTO review needed - see SESSION_STATUS.md Open Questions section"

### With Claude in Chrome (Ops Lead)
**Handoff Pattern:**
- You write code → commit to GitHub → document in SESSION_STATUS.md
- Chrome instance reads status → knows to deploy/test in Shopify admin
- Chrome instance reports results → you fix bugs in next session

**Example:**
```markdown
**Completed:**
- Created Shopify metafield definition for logomark.product_id

**Next Session Goals (for Chrome):**
- Test metafield creation in daznia-z0.myshopify.com admin
- Verify metafield appears on product edit page
- Report any Shopify admin UI issues
```

---

## Debugging Common Issues

### "I can't find the file Chris mentioned"
- Check `git pull origin main` (may need to fetch latest)
- Check current directory: `pwd` (should be repo root)
- Ask Chris to confirm file name/path

### "The SOAP library isn't working"
- Verify credentials.json exists (gitignored file - Chris must provide)
- Check network access (are you behind firewall?)
- Review SOAP_CLIENT_SETUP.md for endpoint details

### "I'm getting merge conflicts"
- Should NOT happen if you're the only one coding
- If Chris manually edited files via GitHub web UI, pull first
- Resolve conflicts → commit → push

---

## Reminders

**You Are Stateless:**
- No memory between sessions
- SESSION_STATUS.md is your only context
- Always start with "Read → Verify → Confirm objective"

**You Are Not Alone:**
- Claude.ai (CTO) makes architecture calls
- Claude in Chrome handles web admin
- Chris has final say on everything

**Your Primary Job:**
- Write clean, tested code
- Document decisions
- Update session status
- Commit early and often

---

**Version:** 1.0  
**Last Updated:** 2026-01-03  
**Owner:** Claude.ai (CTO)
