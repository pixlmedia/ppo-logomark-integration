# PPO LogoMark Integration - Multi-Claude Setup Guide

**For:** Chris (Project Owner)  
**Purpose:** How to work with Claude.ai (CTO), Claude Code (Dev), and Claude in Chrome (Ops)  
**Date:** 2026-01-03

---

## Quick Overview

You now have a **three-instance Claude system** coordinating on this project:

| Instance | Role | Tools | Primary Responsibilities |
|----------|------|-------|-------------------------|
| **Claude.ai** (this chat) | CTO / Architect | Web search, docs review | Architecture decisions, cross-system coordination, strategic planning |
| **Claude Code** (VS Code) | Dev Lead | File ops, git, bash | Code implementation, testing, status tracking |
| **Claude in Chrome** | Ops Lead | Browser control | Shopify admin, Make.com scenarios, web UI testing |

All three read from the same GitHub repository and follow the same session discipline.

---

## How It Works

### The "No Memory" Rule
- **None of the Claude instances remember anything between sessions**
- All context lives in GitHub markdown files
- Each session starts by reading `SYSTEM_CHARTER.md` → `SESSION_STATUS.md` → `IMPLEMENTATION_CHECKLIST.md`

### The Single Source of Truth
- **GitHub repository** = the brain of the system
- SESSION_STATUS.md = what happened last session, what's next
- IMPLEMENTATION_CHECKLIST.md = detailed task breakdown
- All other docs = reference material

### The Workflow
```
Session Start:
  ↓
Claude Code reads status files
  ↓
You tell Claude Code what to work on
  ↓
Claude Code writes code, commits, updates status
  ↓
Session End:
  ↓
Claude Code commits everything + updates SESSION_STATUS.md
  ↓
Next session starts fresh (reads updated status)
```

---

## Your Role (Chris)

### At Session Start
1. Open VS Code with repo
2. Start Claude Code session
3. Tell Claude Code the objective for today (or say "continue where we left off")
4. Claude Code reads status files and confirms plan
5. You approve and let it work

### During Session
- Answer questions Claude Code asks (tech stack choices, missing info)
- Review code/decisions in real-time if you want
- Redirect if Claude Code goes off-track

### At Session End
- Claude Code will update SESSION_STATUS.md
- Claude Code commits everything
- You can review the commit in GitHub to see what changed

### When You Need Architecture Decisions
- Ask me (Claude.ai in this chat) for strategic guidance
- I'll review the docs and give recommendations
- You make final call

### When You Need Web Admin Work
- Open Claude in Chrome
- Tell it to read SESSION_STATUS.md
- Give it Shopify/Make.com tasks
- It reports results back (you paste into VS Code session for Claude Code to see)

---

## Installation Checklist

### ✅ Already Done
- [x] Claude in Chrome installed and working
- [x] GitHub repo created with all docs
- [x] Core documentation written (SYSTEM_CHARTER, PROJECT_BRIEFING, ARCHITECTURE)

### ⏳ Do This Next
- [ ] **Install Claude Code in VS Code**
  - Download from: https://code.visualstudio.com/docs/copilot/claude-code (or search "Claude Code VS Code extension")
  - Authenticate with your Anthropic account
  - Open the `ppo-logomark-integration` repo folder in VS Code
  
- [ ] **Test Claude Code Setup**
  - Open VS Code integrated terminal
  - Type `claude` (or use the extension UI)
  - Ask Claude Code to read SYSTEM_CHARTER.md
  - Verify it can access the files

- [ ] **Clone Repo Locally** (if not already done)
  ```bash
  git clone [your-repo-url] ppo-logomark-integration
  cd ppo-logomark-integration
  ```

- [ ] **Choose SOAP Client Tech Stack**
  - Decision needed: Node.js or PHP?
  - Consider: What have you built similar things with via ChatGPT?
  - Tell Claude Code your choice in next session

---

## Typical Session Flow

### Example: Starting a Code Session

**You (in VS Code with Claude Code):**
> "We're in Phase 1. Please read SESSION_STATUS.md and IMPLEMENTATION_CHECKLIST.md and tell me what we should work on next."

**Claude Code:**
> "I've reviewed the status. Last session completed the documentation setup. The next tasks in Phase 1 are:
> 1. Wait for LogoMark field spec (blocker)
> 2. Choose SOAP client tech stack (needs your decision)
> 3. Set up development environment (can start once tech stack chosen)
>
> Have you received the field spec yet? And which tech stack do you prefer - Node.js or PHP?"

**You:**
> "No field spec yet. Let's go with Node.js. Set up the development environment for Node.js SOAP client."

**Claude Code:**
> "Got it. I'll set up the Node.js environment. This includes:
> - Creating package.json
> - Installing `soap` library
> - Setting up folder structure
> - Creating config template
>
> Should I proceed?"

**You:**
> "Yes, go ahead."

_(Claude Code does the work, shows you progress, commits at end)_

---

### Example: Handing Off to Chrome Instance

**End of Code Session:**
- Claude Code updates SESSION_STATUS.md with: "Created metafield definition for logomark.product_id in code. Next: Chrome instance needs to test in Shopify admin."

**Next Day - You Open Chrome:**
> "Please read SESSION_STATUS.md and complete the Shopify admin tasks listed."

**Claude in Chrome:**
> "I see the last Code session created metafield definitions. I'll now:
> 1. Navigate to Shopify admin → Settings → Custom Data
> 2. Verify metafield appears
> 3. Test on a product
> 4. Report results
>
> Proceeding..."

_(Chrome does the work, reports back)_

**You paste Chrome's report into Code session for next time.**

---

## Decision Authority Matrix

| Decision Type | Who Decides | Process |
|--------------|-------------|---------|
| **Tech stack choice** | Chris | Claude Code asks, you choose |
| **Code implementation details** | Claude Code | Follows architecture, doesn't need approval for details |
| **Architecture changes** | Claude.ai (CTO) with Chris approval | Code flags issue → CTO reviews → Chris decides |
| **Shopify admin changes** | Chrome (with oversight) | Follows checklist, asks if unsure |
| **Scope changes** | Chris only | Update PROJECT_BRIEFING.md → all instances re-read |

---

## File Upload/Download Workflow

### Uploading Files to GitHub
**Option 1 (Web UI):**
- Go to GitHub repo in browser
- Click "Add file" → "Upload files"
- Drag file (e.g., LogoMark Excel spec)
- Commit

**Option 2 (VS Code):**
- Save file to repo folder
- Claude Code will see it and can commit it
- `git add [file]` → `git commit -m "..."` → `git push`

### Downloading Code from GitHub
**Option 1 (VS Code):**
- Files are already local (you cloned the repo)
- Just open the file in VS Code

**Option 2 (GitHub Web UI):**
- Click on file → "Raw" → Copy or download

---

## Communication Between Instances

### Instance Handoffs
Instances **do NOT talk to each other directly**. You are the bridge.

**Pattern:**
1. Code session ends → updates SESSION_STATUS.md
2. You commit/push changes
3. Chrome session starts → reads updated SESSION_STATUS.md
4. Chrome knows what Code did and what it needs to do

### Coordination Example
**Scenario:** Code creates new product structure, Chrome needs to test it in Shopify.

**Code's Commit:**
```
Created product template in src/shopify-integration/product-template.json

Next steps (for Chrome instance):
- Apply template to test product in daznia-z0.myshopify.com
- Verify all fields render correctly
- Report any Shopify UI issues
```

**Chrome's Session (next day):**
> (Reads status) "I see Code created product-template.json. I'll apply it to a test product now..."

---

## Troubleshooting Common Issues

### "Claude Code says it can't find a file"
- Verify you opened the **repo folder** in VS Code (not parent folder)
- Try `pwd` in terminal to check current directory
- Pull latest changes: `git pull origin main`

### "Claude in Chrome can't access Shopify admin"
- Make sure you're logged into Shopify in that browser
- Verify Chrome extension has permissions for myshopify.com domains
- Check if store URL is correct: daznia-z0.myshopify.com

### "Claude Code wants to make an architecture change"
- It should flag this and ask for CTO review
- Switch to Claude.ai chat (me)
- Share the proposed change
- I'll review and recommend
- You decide

### "I lost track of where we are in the project"
- Open SESSION_STATUS.md
- Read "Current State Summary" section
- Read most recent session entry
- That's the full context

---

## Best Practices

### Do's
✅ Always let Claude Code read status files at session start  
✅ Commit frequently (end of each session minimum)  
✅ Update SESSION_STATUS.md when significant progress happens  
✅ Ask questions when stuck (better to ask than assume)  
✅ Use Claude.ai (me) for strategic decisions  

### Don'ts
❌ Don't assume Claude remembers previous sessions  
❌ Don't skip status file updates (you'll lose context)  
❌ Don't let Code make major architecture changes without CTO review  
❌ Don't work on code outside VS Code without committing (keep Git in sync)  
❌ Don't overthink it - the system is designed to be simple  

---

## Next Steps (Right Now)

1. **Install Claude Code** in VS Code (15 min)
2. **Test the setup** - ask Claude Code to read SYSTEM_CHARTER.md (5 min)
3. **Make tech stack decision** - Node.js or PHP? (2 min)
4. **Wait for field spec** - check with Michael on ETA (ongoing)
5. **Start Phase 1 coding** - once field spec arrives (Claude Code will guide you)

---

## Quick Reference

| Need | Use |
|------|-----|
| Code implementation | Claude Code (VS Code) |
| Strategic planning | Claude.ai (this chat) |
| Shopify admin work | Claude in Chrome |
| Architecture decisions | Claude.ai → Chris approves |
| Task status | SESSION_STATUS.md |
| Task details | IMPLEMENTATION_CHECKLIST.md |
| Rules & governance | SYSTEM_CHARTER.md |

---

**Questions?** Ask me (Claude.ai) anything about this setup. I'm your CTO and I'm here to make sure the system works smoothly.

**Ready to start?** Install Claude Code and we'll begin Phase 1 as soon as the LogoMark field spec arrives.
