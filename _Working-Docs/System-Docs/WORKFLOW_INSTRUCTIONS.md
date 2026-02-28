# WORKFLOW INSTRUCTIONS
**Standard Operating Procedures for Claude Code in Side-Brands**

**Purpose:** How to execute common tasks in this workspace
**Last Updated:** November 2, 2025

---

## 🚀 SLASH COMMANDS (NEW!)

**For streamlined workflows, use these slash commands:**

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/create-brand` | Complete brand creation workflow (Phase 0-1) | Starting a new brand |
| `/build-website` | Website development (Phase 2 sub-phases) | After Phase 1 complete |
| `/deploy-brand` | Deployment + PMF validation (Phase 3) | After website built |
| `/launch-brand` | Official launch + growth (Phase 4) | After PMF validated |
| `/check-brand-status` | Get detailed brand status report | Anytime - checking progress |

**How to use:** Simply type the command (e.g., `/create-brand`) and Claude will load the appropriate workflow.

---

## 🎯 COMMON TASKS & WORKFLOWS

### Task 1: Starting Work on a New Brand

**User asks:** "Let's build [Brand Name]" or "Create a new brand"

**RECOMMENDED:** Use `/create-brand` slash command

**Manual Workflow (if not using command):**

**Workflow:**
```bash
# 1. Check if brand folder exists
cd "[Brand Name]/"

# 2. If exists, check what files are present
# List all .md files

# 3. Set up progress tracking
cd ../Brand-Progress-Tracking/Active-Brands/
mkdir "[Brand Name]"
cd "[Brand Name]"

# 4. Copy tracking templates
copy ../../Templates/BRAND_PROGRESS_TRACKER.md .
copy ../../Templates/GOALS_AND_RESULTS_TRACKER.md .
copy ../../Templates/WEEKLY_TASK_TRACKER.md .

# 5. Fill in brand information in trackers

# 6. Start Phase 1: Planning
# Follow: 00-MASTER-BRAND-SYSTEM-GUIDE.md
```

**Deliverables:**
- Progress tracking set up
- Planning documents complete
- Next steps clear

---

### Task 2: Checking Brand Status

**User asks:** "What's the status of [Brand Name]?" or "Which brands are ready?"

**Workflow:**
```bash
# 1. Check brand folder
cd "[Brand Name]/"
# List all files

# 2. Verify against required files (14 total):
- Brand Info.md
- Brand Foundation.md
- TECHNICAL_ARCHITECTURE.md
- Affiliate Monetization Plan.md
- Startup_Brand_SOP_Template.md
- Startup_Marketing_Plan_System.md
- Complete Brand Template.md
- YouTube.md, Instagram.md, Facebook.md, Twitter.md, TikTok.md, Reddit.md
- Website/ folder (if building)

# 3. Create status summary in _Working-Docs/
cd ../../_Working-Docs/
# Create analysis file here

# 4. Report findings to user
```

**Deliverables:**
- Accurate brand status
- List of missing files
- Recommendations for next steps

---

### Task 3: Cleaning Up / Organizing Files

**User asks:** "Clean up the files" or "Organize this folder"

**Workflow:**
```bash
# 1. Audit current state
# List all files in target folder

# 2. Create analysis in _Working-Docs/
cd _Working-Docs/
# Create file: [folder-name]-cleanup-plan.md

# 3. Categorize files:
- Essential (keep)
- Reference (keep but organize)
- Redundant (archive or delete)
- Outdated (delete)

# 4. Get user approval before deleting

# 5. Execute organization
# Move/delete files

# 6. Verify changes worked
# List files after cleanup

# 7. Update .claude/MASTER_BRAND_STATUS if needed
```

**Deliverables:**
- Clean, organized folder
- Documentation of what was done
- Updated knowledge base

---

### Task 4: Building a Brand Website

**User asks:** "Build website for [Brand Name]"

**RECOMMENDED:** Use `/build-website` slash command

**Manual Workflow (if not using command):**

**Workflow:**
```bash
# 1. Verify planning is complete
cd "[Brand Name]/"
# Check for all 14 planning files

# 2. Create website folder
mkdir Website
cd Website

# 3. Follow UNIVERSAL_TECH_STACK_GUIDE.md
# Set up Next.js, Prisma, Sanity, etc.

# 4. Update progress tracker
cd ../../Brand-Progress-Tracking/Active-Brands/[Brand Name]/
# Update BRAND_PROGRESS_TRACKER.md
# Mark "Website Development" as in_progress

# 5. Build iteratively
# Follow Phase 2 in 00-MASTER-BRAND-SYSTEM-GUIDE.md

# 6. Deploy when ready
# Follow MASTER_DEPLOYMENT_CHECKLIST.md
```

**Deliverables:**
- Functional website
- Progress tracker updated
- Deployment complete

---

### Task 5: Updating Brand Status

**User says:** "Brand X is now complete" or "We finished the website"

**Workflow:**
```bash
# 1. Update progress tracker
cd Brand-Progress-Tracking/Active-Brands/[Brand Name]/
# Update BRAND_PROGRESS_TRACKER.md

# 2. Update master status
cd ../../.claude/
# Edit MASTER_BRAND_STATUS_AND_SYSTEM.md
# Update brand completion percentage

# 3. If brand is launched, move to completed
cd ../Brand-Progress-Tracking/
mv Active-Brands/[Brand Name]/ Completed-Brands/

# 4. Create completion summary in brand folder
cd ../[Brand Name]/
# Create LAUNCH_SUMMARY.md with results
```

**Deliverables:**
- Updated status documentation
- Brand moved to completed (if launched)
- Launch summary created

---

### Task 6: Creating New Templates/Guides

**User asks:** "Create a guide for [topic]"

**Workflow:**
```bash
# 1. Create draft in _Working-Docs/
cd _Working-Docs/
# Create [topic]-guide-draft.md

# 2. Get user feedback

# 3. Finalize location:

# If it's a brand-building guide:
mv [topic]-guide-draft.md "../1. Brand System Template & Tech Stack Template/[TOPIC]_GUIDE.md"

# If it's system documentation:
mv [topic]-guide-draft.md "../.claude/[TOPIC].md"

# If it's brand-specific:
mv [topic]-guide-draft.md "../[Brand Name]/[topic]-guide.md"

# 4. Update START_HERE.md if it's in template folder

# 5. Update .claude knowledge base if significant
```

**Deliverables:**
- New guide in correct location
- Index files updated
- Knowledge base updated

---

### Task 7: Answering Questions About the System

**User asks:** "How do I [do something]?" or "Where is [file]?"

**Workflow:**
```bash
# 1. Check .claude folder first
cd .claude/
# Read relevant documentation

# 2. Check template folder guides
cd "../1. Brand System Template & Tech Stack Template/"
# Read 00-MASTER-BRAND-SYSTEM-GUIDE.md or other guides

# 3. If creating analysis, use _Working-Docs/
cd ../_Working-Docs/
# Create analysis file if needed

# 4. Answer with file references
# Example: "See .claude/MASTER_BRAND_STATUS_AND_SYSTEM.md:145"
```

**Deliverables:**
- Accurate answer
- File references
- Next action steps

---

## 📂 FILE LOCATION QUICK GUIDE

### Where to create files during tasks:

| Task | File Location |
|------|---------------|
| Analysis/planning | `_Working-Docs/` |
| Brand planning docs | `[Brand Name]/` |
| Progress tracking | `Brand-Progress-Tracking/Active-Brands/[Brand Name]/` |
| Website code | `[Brand Name]/Website/` |
| System updates | `.claude/` |
| New templates | `1. Brand System Template & Tech Stack Template/` (rarely) |

---

## 🔄 STANDARD WORKFLOW PATTERN

For ANY task:

1. **Read** relevant `.claude/` documentation
2. **Create** working files in `_Working-Docs/` (if needed)
3. **Execute** task following guides
4. **Update** progress trackers
5. **Clean up** working files
6. **Update** `.claude/` if system changed

---

## ⚠️ IMPORTANT REMINDERS

### Always:
- ✅ Read `.claude/` files on session start
- ✅ Use `_Working-Docs/` for temporary files
- ✅ Update progress trackers when working on brands
- ✅ Follow folder structure rules
- ✅ Verify file operations succeeded (don't assume)
- ✅ Keep root folder clean

### Never:
- ❌ Create files directly in root
- ❌ Skip reading knowledge base
- ❌ Assume file moves worked (verify!)
- ❌ Create duplicate documentation
- ❌ Leave temp files cluttering folders

---

## 📋 PRE-DELETE CHECKLIST

Before deleting ANY files:

1. ✅ Create deletion plan in `_Working-Docs/`
2. ✅ Verify files are truly redundant
3. ✅ Check information isn't unique
4. ✅ Get user approval
5. ✅ Execute deletion
6. ✅ Verify deletion succeeded
7. ✅ Update documentation

---

## 🎯 SUCCESS METRICS

You're doing it right if:
- Root folder stays clean (only folders + README.md)
- `_Working-Docs/` is used for temp work
- Progress trackers are up to date
- `.claude/` knowledge base is current
- File operations are verified
- User gets accurate information

---

## 💡 TIPS FOR EFFICIENCY

1. **Batch read** `.claude/` files at start
2. **Use TodoWrite** to track multi-step tasks
3. **Verify file operations** with Glob or dir
4. **Reference files** with line numbers
5. **Clean up** `_Working-Docs/` when done
6. **Update status** proactively

---

## 🆘 TROUBLESHOOTING

### "I don't know where a file is"
→ Check `.claude/MASTER_BRAND_STATUS_AND_SYSTEM.md`

### "File move didn't work"
→ Use Glob to verify, try again with correct path

### "User asked about brand status"
→ Read `.claude/MASTER_BRAND_STATUS_AND_SYSTEM.md` first

### "Not sure which guide to follow"
→ Check `1. Brand System Template & Tech Stack Template/START_HERE.md`

### "Creating temporary analysis"
→ Use `_Working-Docs/` folder

---

**Follow these workflows and the system stays organized! 🎯**

---

*Last Updated: October 2025*
*Part of the .claude knowledge base*
