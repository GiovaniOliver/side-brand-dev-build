# SYSTEM RULES & FOLDER STRUCTURE
**For Claude Code Sessions in Side-Brands Directory**

**Purpose:** Instructions for AI assistants working in this workspace
**Last Updated:** November 2, 2025

---

## 🚨 CRITICAL RULES - READ FIRST

### Rule 1: KEEP ROOT FOLDER CLEAN
**NEVER create files directly in the Side-Brands root folder!**

### Rule 2: Know Where Files Go
Every file has a designated location. Follow the structure below.

### Rule 3: Always Check .claude Folder First
When starting a new session, read these files:
1. `.claude/SYSTEM_RULES_AND_STRUCTURE.md` (this file)
2. `.claude/AGENT_USAGE_REQUIREMENTS.md` (CRITICAL - how to use sub-agents)
3. `.claude/MASTER_BRAND_STATUS_AND_SYSTEM.md` (brand status)
4. `.claude/WORKFLOW_INSTRUCTIONS.md` (how to work)
5. `.claude/Viral_Framework.md` (Village Capital framework)

### Rule 4: ALWAYS Use Sub-Agents for Tasks
**MANDATORY:** Use the Task tool with specialized sub-agents for ALL brand development work.
- See `.claude/AGENT_USAGE_REQUIREMENTS.md` for complete guide
- Phase 2 sub-phases (2B, 2C, 2D) MUST run in parallel using agents
- Every major feature should be built by a specialized agent
- DO NOT manually code features - use agents!

### Rule 5: Use Slash Commands for Brand Workflows
Available commands:
- `/create-brand` - Start new brand creation (Phase 0-1)
- `/build-website` - Build website (Phase 2)
- `/deploy-brand` - Deploy and validate PMF (Phase 3)
- `/launch-brand` - Official launch (Phase 4)
- `/check-brand-status` - Get brand status report

---

## 📁 MANDATORY FOLDER STRUCTURE

```
Side-Brands/
│
├── .claude/                                    ← KNOWLEDGE BASE (read on start)
│   ├── SYSTEM_RULES_AND_STRUCTURE.md         ← This file - folder rules
│   ├── MASTER_BRAND_STATUS_AND_SYSTEM.md     ← Brand status & system
│   └── WORKFLOW_INSTRUCTIONS.md               ← How to execute tasks
│
├── .claude/commands/                            ← SLASH COMMANDS (NEW)
│   ├── create-brand.md                         ← Brand creation workflow
│   ├── build-website.md                        ← Website build workflow
│   ├── deploy-brand.md                         ← Deployment workflow
│   ├── launch-brand.md                         ← Launch workflow
│   └── check-brand-status.md                   ← Status checking
│
├── _working-docs/                              ← TEMPORARY WORKING FILES (lowercase)
│   ├── [Brand Name]/                           ← Per-brand research folders
│   │   ├── viral-stage-tracking.md            ← Track Viral Levels 1-9
│   │   ├── market-research.md                 ← Market analysis
│   │   ├── phase-progress.md                  ← Phase tracking
│   │   └── brand-creation-notes.md            ← General notes
│   ├── [Planning documents]                    ← Created during sessions
│   ├── [Analysis files]                        ← Temporary analysis
│   └── [Delete or archive when done]           ← Clean up after completion
│
├── _logs/                                      ← SESSION LOGS (lowercase with _)
│   └── [Claude Code session logs]             ← Hook logs, etc.
│
├── 1. Brand System Template & Tech Stack Template/
│   ├── 00-MASTER-BRAND-SYSTEM-GUIDE.md        ← START HERE - Complete guide
│   ├── VIRAL_FRAMEWORK_INTEGRATION.md         ← NEW: Viral Framework mapping
│   ├── _working-docs/VIRAL_FRAMEWORK_ANALYSIS_AND_INTEGRATION.md  ← Detailed analysis
│   ├── Startup_Brand_SOP_Template.md
│   ├── Startup_Marketing_Plan_System.md
│   └── [Other essential files]
│
├── Brand-Progress-Tracking/                    ← BRAND PROGRESS TRACKING
│   ├── Active-Brands/                          ← Brands being built
│   ├── Completed-Brands/                       ← Launched brands
│   ├── On-Hold-Brands/                         ← Paused brands
│   └── Templates/                              ← Tracking templates
│
├── [Brand Folders]/                            ← INDIVIDUAL BRANDS (18 total)
│   ├── Defi Baddies/
│   ├── Now Hired US/
│   └── etc...
│
└── README.md                                   ← Root folder overview only
```

---

## 📝 FILE CREATION RULES

### Where to Create Files:

#### Working/Planning Documents
**Location:** `_Working-Docs/`
**Examples:**
- Analysis documents
- Planning files
- Session notes
- Temporary reports
- Cleanup plans
- Organization plans

**Rule:** Create ALL temporary/working files here!

---

#### Brand-Specific Files
**Location:** `[Brand Name]/` folder
**Examples:**
- Brand Info.md
- Brand Foundation.md
- TECHNICAL_ARCHITECTURE.md
- Social media strategies
- Website folder

**Rule:** Files specific to ONE brand go in that brand's folder!

---

#### Progress Tracking
**Location:** `Brand-Progress-Tracking/Active-Brands/[Brand Name]/`
**Examples:**
- BRAND_PROGRESS_TRACKER.md
- GOALS_AND_RESULTS_TRACKER.md
- WEEKLY_TASK_TRACKER.md

**Rule:** When working on a brand, track progress here!

---

#### Template Updates
**Location:** `1. Brand System Template & Tech Stack Template/`
**Only update existing files!** Don't create new ones here.

**Rule:** This folder should stay at ~27 essential files!

---

#### Knowledge Base Updates
**Location:** `.claude/`
**Examples:**
- System updates
- New workflow instructions
- Updated brand status

**Rule:** Update these when system/status changes significantly!

---

## 🚫 WHAT NOT TO DO

### DON'T:
- ❌ Create files in Side-Brands root (use `_Working-Docs/` instead)
- ❌ Create random folders in root
- ❌ Add files to template folder without reason
- ❌ Create duplicate documentation
- ❌ Leave temp files cluttering folders

### DO:
- ✅ Use `_Working-Docs/` for all temporary work
- ✅ Check `.claude/` folder on session start
- ✅ Follow the folder structure
- ✅ Clean up working docs when done
- ✅ Update `.claude/MASTER_BRAND_STATUS_AND_SYSTEM.md` when brand status changes

---

## 🎯 SESSION START PROCEDURE

When you start a new Claude Code session in Side-Brands:

### Step 1: Read Knowledge Base (1-2 min)
```bash
# Read these files in order:
1. .claude/SYSTEM_RULES_AND_STRUCTURE.md       # Folder rules (this file)
2. .claude/MASTER_BRAND_STATUS_AND_SYSTEM.md   # Brand status
3. .claude/WORKFLOW_INSTRUCTIONS.md            # How to work
```

### Step 2: Understand User Request

### Step 3: Create Working Files in Right Place
```bash
# If you need to create analysis/planning files:
cd _Working-Docs/
# Create your files here

# If working on a specific brand:
cd "[Brand Name]/"
# Work here

# If tracking progress:
cd Brand-Progress-Tracking/Active-Brands/[Brand Name]/
# Update tracking files here
```

### Step 4: Execute Task Following Workflow

### Step 5: Clean Up
- Move final deliverables to proper locations
- Archive or delete temp files from `_Working-Docs/`
- Update `.claude/` knowledge base if needed

---

## 📋 QUICK REFERENCE

### "Where do I create...?"

| File Type | Location |
|-----------|----------|
| Analysis/Planning docs | `_Working-Docs/` |
| Brand-specific files | `[Brand Name]/` |
| Progress tracking | `Brand-Progress-Tracking/Active-Brands/[Brand Name]/` |
| System documentation | `.claude/` |
| Templates | Don't create - use existing in template folder |

---

## 🔄 UPDATING THIS SYSTEM

### When to Update `.claude/` Files:

**Update MASTER_BRAND_STATUS_AND_SYSTEM.md when:**
- Brand completes a phase
- New brand added
- Brand status changes
- Website launched

**Update SYSTEM_RULES_AND_STRUCTURE.md when:**
- Folder structure changes
- New organization rules added
- Workflow changes

**Update WORKFLOW_INSTRUCTIONS.md when:**
- New processes added
- Better workflows discovered
- Tools/tech stack changes

---

## 📊 CURRENT STATUS SUMMARY

**Brands:** 18 total
- Planning complete: ~16 brands
- Websites in progress: 2 brands
- Priority: Defi Baddies, Now Hired US

**Template Folder:** Being cleaned (56 redundant files to delete)

**Tracking System:** Set up in `Brand-Progress-Tracking/`

**Full Details:** See `.claude/MASTER_BRAND_STATUS_AND_SYSTEM.md`

---

## 🎓 FOR CLAUDE CODE SESSIONS

**You are working in a brand development system with:**
- 18 side brands in development
- Defined workflow and structure
- Progress tracking system
- Clear folder organization

**Your job:**
1. Read `.claude/` files on start
2. Follow folder structure rules
3. Keep root folder clean
4. Use `_Working-Docs/` for temp files
5. Update knowledge base when needed

**Questions? Check:**
- This file for structure rules
- `MASTER_BRAND_STATUS_AND_SYSTEM.md` for brand info
- `WORKFLOW_INSTRUCTIONS.md` for how to work

---

**Keep this system organized and you'll work efficiently! 🚀**

---

*Last Updated: October 2025*
*Maintained by: Oliver Productions*
