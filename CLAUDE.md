# SMG Side Brands - Development
**Last Updated:** March 27, 2026

## Overview

Documentation workspace for all 21 SMG side brand projects. Contains brand foundations, marketing plans, SOPs, affiliate strategies, and system documentation. No website code — docs only.

## System Documentation

All system docs are in `docs/`:

| File | Purpose |
|------|---------|
| `docs/MASTER_BRAND_STATUS_AND_SYSTEM.md` | **Single source of truth** — brand status, contradictions resolved, correct revenue targets |
| `docs/SYSTEM_RULES_AND_STRUCTURE.md` | Folder structure rules and session procedures |
| `docs/WORKFLOW_INSTRUCTIONS.md` | Standard operating procedures |
| `docs/AGENT_USAGE_REQUIREMENTS.md` | How to use sub-agents for brand work |
| `docs/Viral_Framework.md` | 9-level viral growth framework |

> ⚠️ `_Working-Docs/System-Docs/` contains older copies of these files. Always use `docs/` as canonical.

## Available Commands

This workspace has custom slash commands in `.claude/commands/`:

| Command | Purpose |
|---------|---------|
| `/create-brand` | Initialize a new brand from template |
| `/build-website` | Build a brand website |
| `/deploy-brand` | Deploy a brand to production |
| `/launch-brand` | Full brand launch sequence |
| `/check-brand-status` | Check build/deploy status |
| `/full-pipeline` | Run complete brand pipeline |
| `/brand-intake` | New brand intake process |

## Brand Portfolio

**21 brands** in `brands/` folder. See `docs/MASTER_BRAND_STATUS_AND_SYSTEM.md` for full status.

### Critical Issues (Resolve Before Launch)
- **Now Hired US**: ClickBank catalog contains adult content — must be removed
- **The Work Out Six Pack Stop**: ClickBank list has dental/prostate supplements — replace with fitness-only
- **Is This Yo BF-GF**: Affiliate Plan describes a surveillance/cheating detection SaaS — rewrite as entertainment brand
- **Hood Fat Albert**: Brand Foundation document is empty placeholders — must be completed

### Deployment Priority
1. PAUSEWELL (95% complete, well-aligned)
2. AI Content Studio Pro (91%, best-aligned)
3. Rappers Looking for Beats (90%)
4. Digital Nomad Gear (91%)

## Related Folders

- **Admin**: `1a.) SMG-Side-Brands Admin Work/` (API keys, brand assets, social media configs)
- **Marketing**: `1c.) SMG-Marketing-Side Brands & Managment/` (campaigns, content, agents)
- **Working Docs**: `_Working-Docs/` (audits, tracking, archives)
