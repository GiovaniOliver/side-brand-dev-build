# ADDITIONAL SLASH COMMAND RECOMMENDATIONS
**Suggested New Commands for Side-Brands System**

**Created:** November 2, 2025
**Purpose:** Recommend additional slash commands to streamline workflows

---

## CURRENTLY IMPLEMENTED COMMANDS ✅

1. `/create-brand` - Brand creation workflow (Phase 0-1)
2. `/build-website` - Website build workflow (Phase 2)
3. `/deploy-brand` - Deployment + PMF validation (Phase 3)
4. `/launch-brand` - Official launch (Phase 4)
5. `/check-brand-status` - Brand status reports

---

## RECOMMENDED ADDITIONAL COMMANDS

### Priority 1: High-Value Commands (Implement First)

#### 1. `/analyze-market [brand-name]`
**Purpose:** Deep market analysis with TAM/SAM/SOM calculation

**What it does:**
- Analyzes target market size
- Calculates Total Addressable Market (TAM)
- Calculates Serviceable Available Market (SAM)
- Calculates Serviceable Obtainable Market (SOM)
- Identifies top 5 competitors
- Analyzes growth trends
- Provides market entry recommendations

**When to use:** Phase 1B (Market Research)

**Agent:** `market-researcher`

**Example workflow:**
```markdown
1. Ask for brand name and niche
2. Launch market-researcher agent
3. Generate comprehensive market analysis
4. Save to [Brand Name]/market-analysis.md
5. Update viral-stage-tracking.md (Level 4)
```

---

#### 2. `/content-strategy [brand-name]`
**Purpose:** Generate complete content strategy

**What it does:**
- Creates 90-day content calendar
- Writes 10+ blog post outlines
- Creates social media content batch (30 days)
- Develops email sequences (welcome, nurture, sales)
- SEO keyword research
- Content pillars and topics

**When to use:** Phase 2F (Content Creation)

**Agent:** `content-marketer` + `seo-specialist`

**Example workflow:**
```markdown
1. Ask for brand name and target keywords
2. Launch content-marketer for strategy
3. Launch seo-specialist for keyword research
4. Generate content calendar
5. Save to [Brand Name]/Website/docs/content-strategy/
```

---

#### 3. `/validate-pmf [brand-name]`
**Purpose:** Run Product-Market Fit validation

**What it does:**
- Sets up PMF metrics dashboard
- Defines success criteria (CAC, LTV, retention)
- Creates PMF survey ("How disappointed would you be...")
- Analyzes early user data
- Calculates PMF score
- Provides recommendations (pivot, persevere, or kill)

**When to use:** Phase 3B (PMF Validation)

**Agent:** `analytics-architect` + `ux-researcher`

**Example workflow:**
```markdown
1. Ask for brand name and current metrics
2. Launch analytics-architect for dashboard setup
3. Launch ux-researcher for survey design
4. Collect and analyze data
5. Generate PMF scorecard
6. Recommend next steps
```

---

#### 4. `/optimize-seo [brand-name]`
**Purpose:** Complete SEO audit and optimization

**What it does:**
- Technical SEO audit (Core Web Vitals, indexing, sitemaps)
- On-page SEO optimization (meta tags, headings, content)
- Keyword gap analysis
- Backlink strategy
- Local SEO (if applicable)
- SEO action plan with priorities

**When to use:** Phase 3C (Pre-Launch Testing) or ongoing

**Agent:** `seo-specialist`

**Example workflow:**
```markdown
1. Ask for brand website URL
2. Launch seo-specialist
3. Run comprehensive SEO audit
4. Generate prioritized fix list
5. Implement high-priority fixes
6. Save SEO report to docs/
```

---

#### 5. `/launch-campaign [brand-name]`
**Purpose:** Create and execute launch campaign

**What it does:**
- Develops launch strategy
- Creates launch content (emails, social posts, PR)
- Sets up Product Hunt submission
- Coordinates influencer outreach
- Creates launch day checklist
- Monitors launch metrics

**When to use:** Phase 4A (Official Launch)

**Agents:** `content-marketer` + `sales-automator`

**Example workflow:**
```markdown
1. Ask for brand name and launch date
2. Launch content-marketer for campaign strategy
3. Launch sales-automator for email sequences
4. Create launch day checklist
5. Execute campaign
6. Monitor and report metrics
```

---

### Priority 2: Utility Commands (Nice to Have)

#### 6. `/compare-brands`
**Purpose:** Compare multiple brands in portfolio

**What it does:**
- Lists all brands with status
- Compares metrics (traffic, revenue, growth)
- Identifies top performers
- Recommends resource allocation
- Shows portfolio diversification

**When to use:** Anytime - portfolio management

**Agent:** `business-analyst`

---

#### 7. `/automate-brand [brand-name]`
**Purpose:** Set up automation for scaling

**What it does:**
- Identifies automation opportunities
- Sets up social media scheduling (Buffer/Hootsuite)
- Configures email automation (ConvertKit/Mailchimp)
- Implements chatbot for support
- Creates content repurposing workflows
- Documents automation playbook

**When to use:** Phase 4B (Scale Preparation)

**Agent:** `platform-engineer` + `sales-automator`

---

#### 8. `/competitor-analysis [brand-name]`
**Purpose:** Deep dive on competitors

**What it does:**
- Identifies top 10 competitors
- Analyzes their strategies (content, SEO, social)
- SWOT analysis for each competitor
- Identifies gaps and opportunities
- Provides differentiation recommendations

**When to use:** Phase 1B (Market Research)

**Agent:** `competitive-analyst` + `market-researcher`

---

#### 9. `/monetization-audit [brand-name]`
**Purpose:** Optimize revenue streams

**What it does:**
- Audits current monetization
- Identifies new revenue opportunities
- Calculates unit economics
- A/B test recommendations
- Pricing optimization
- Affiliate program analysis

**When to use:** Phase 5 (Scale) or when revenue plateaus

**Agent:** `business-analyst` + `sales-automator`

---

#### 10. `/backup-brand [brand-name]`
**Purpose:** Create complete brand backup

**What it does:**
- Exports all documentation
- Backs up website code
- Saves database snapshot
- Archives content
- Creates restoration guide
- Uploads to cloud storage

**When to use:** Before major changes or regularly

**Agent:** `platform-engineer`

---

### Priority 3: Advanced Commands (Future)

#### 11. `/pivot-brand [brand-name]`
**Purpose:** Execute strategic pivot

**What it does:**
- Analyzes why current approach isn't working
- Identifies pivot opportunities
- Creates pivot plan
- Updates all documentation
- Manages transition

**When to use:** When PMF fails or market shifts

**Agent:** `product-manager` + `business-analyst`

---

#### 12. `/sell-brand [brand-name]`
**Purpose:** Prepare brand for sale/exit

**What it does:**
- Valuation analysis
- Creates exit deck/prospectus
- Identifies potential buyers
- Prepares due diligence documents
- Lists on marketplaces (Flippa, Empire Flippers)
- Handles transfer process

**When to use:** Phase 5C (Exit Planning)

**Agent:** `business-analyst` + `legal-advisor`

---

#### 13. `/clone-brand [source-brand] [new-brand-name]`
**Purpose:** Duplicate successful brand to new niche

**What it does:**
- Copies all documentation
- Adapts to new niche/market
- Updates branding and content
- Replicates tech stack
- Creates launch plan for clone

**When to use:** When a brand proves successful and you want to replicate

**Agent:** `fullstack-developer` + `content-marketer`

---

#### 14. `/merge-brands [brand-1] [brand-2]`
**Purpose:** Consolidate two brands

**What it does:**
- Analyzes synergies
- Creates merge plan
- Combines content and audiences
- Handles technical consolidation
- Manages redirects and SEO

**When to use:** Portfolio optimization

**Agent:** `system-architecture-advisor` + `seo-specialist`

---

## IMPLEMENTATION PRIORITY

### Implement Immediately (This Week)
1. `/analyze-market` - Critical for Phase 1B
2. `/validate-pmf` - Critical for Phase 3B
3. `/content-strategy` - High value for Phase 2F

### Implement Soon (This Month)
4. `/optimize-seo` - Important for growth
5. `/launch-campaign` - Important for Phase 4A
6. `/compare-brands` - Useful for management

### Implement Later (As Needed)
7-14. All other commands as use cases arise

---

## COMMAND CREATION TEMPLATE

When creating a new slash command, use this structure:

```markdown
# [Command Name] - [Brief Description]

You are now in **[Mode Name]** for [context].

## YOUR MISSION
[1-2 sentence mission statement]

## PREREQUISITES
Before starting, verify:
1. [Requirement 1]
2. [Requirement 2]
3. [Requirement 3]

## CONTEXT YOU HAVE
Reference these documents:
- [Document 1]
- [Document 2]

## WORKFLOW

### Step 1: [Step Name]
[Details]

### Step 2: [Step Name]
[Details]

[Continue for all steps...]

## IMPORTANT RULES
1. [Rule 1]
2. [Rule 2]

## SUCCESS CRITERIA
[What does success look like?]

---

**Start by asking: "[Initial question to user]"**
```

---

## SLASH COMMAND BEST PRACTICES

### DO:
- ✅ Make commands focused on a single workflow
- ✅ Use appropriate specialized agents
- ✅ Provide clear step-by-step guidance
- ✅ Include prerequisites and context
- ✅ Define success criteria
- ✅ Save deliverables to proper locations

### DON'T:
- ❌ Make commands too broad/generic
- ❌ Skip agent usage (do it manually)
- ❌ Forget to track progress with TodoWrite
- ❌ Leave deliverables unorganized
- ❌ Skip validation steps

---

## USAGE TRACKING

After implementing new commands, track:
- Usage frequency
- User satisfaction
- Time saved
- Quality of outputs
- Issues encountered

This helps prioritize which commands to build next.

---

## SUMMARY

**Current Commands:** 5
**Recommended to Add:** 14
**Highest Priority:**
1. `/analyze-market`
2. `/validate-pmf`
3. `/content-strategy`

**Benefits:**
- Streamlined workflows
- Consistent quality
- Time savings
- Better guidance
- Automated best practices

---

**Ready to implement? Start with `/analyze-market` - it fills a critical gap in Phase 1B!**

---

*Last Updated: November 2, 2025*
*Maintained by: Oliver Productions*
