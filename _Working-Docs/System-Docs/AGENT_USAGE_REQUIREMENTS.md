# AGENT USAGE REQUIREMENTS
**Critical System Rule for Claude Code Sessions**

**Last Updated:** November 2, 2025
**Priority:** HIGHEST - Must follow in ALL sessions

---

## 🚨 MANDATORY RULE: USE SUB-AGENTS FOR ALL TASKS

**CRITICAL:** When working on brand development tasks, you MUST use the appropriate specialized sub-agents through the Task tool. This is not optional - it's a core requirement of the system.

---

## WHY USE SUB-AGENTS?

### 1. Specialization & Quality
Each agent is optimized for specific tasks with deep expertise:
- **frontend-developer**: React, Next.js, UI/UX best practices
- **python-pro**: Backend, API development, data processing
- **content-marketer**: SEO, copywriting, content strategy
- **sales-automator**: Email sequences, sales copy, conversion optimization
- **seo-specialist**: Technical SEO, on-page optimization, rankings

### 2. Parallel Execution
Phase 2 sub-phases (2B, 2C, 2D) are designed to run in PARALLEL:
```
Week 3-4: Backend Development (python-pro) ║
Week 4-6: Frontend Development (frontend-developer) ║ PARALLEL
Week 6: Monetization Integration (sales-automator) ║
```

Running these in parallel reduces 6 weeks of work to 3-4 weeks!

### 3. Consistent Quality
Specialized agents ensure:
- Best practices followed
- Security considerations addressed
- Performance optimized
- Code quality maintained

---

## WHEN TO USE WHICH AGENT

### Phase 0-1: Planning & Foundation
**Agents to use:**
- `market-researcher` - Market analysis, TAM/SAM/SOM calculations
- `business-analyst` - Business model validation, financial projections
- `product-manager` - Product roadmap, feature prioritization
- `content-marketer` - Content strategy, brand positioning

**Example:**
```javascript
// When doing market research in Phase 1B
Task tool → market-researcher → "Analyze the [niche] market, calculate TAM/SAM/SOM, identify top 5 competitors"
```

---

### Phase 2A: Technical Setup
**Agent:** `platform-engineer` or `fullstack-developer`

**Tasks:**
- Next.js project creation
- Git/GitHub setup
- Folder structure
- Environment configuration

**Example:**
```javascript
Task tool → platform-engineer → "Set up Next.js 14 project with TypeScript, Tailwind, and Prisma for [Brand Name]"
```

---

### Phase 2B: Backend Development
**Agent:** `python-pro` or `secure-backend-dev`

**Tasks:**
- Database schema design
- API routes
- Authentication
- Data validation

**Example:**
```javascript
Task tool → secure-backend-dev → "Build secure authentication system with NextAuth.js, implement user roles and permissions"
```

---

### Phase 2C: Frontend Development
**Agent:** `frontend-developer` or `react-specialist`

**Tasks:**
- Component library
- Page development
- Responsive design
- UI/UX implementation

**Example:**
```javascript
Task tool → frontend-developer → "Build responsive homepage with hero section, features, testimonials using shadcn/ui"
```

---

### Phase 2D: Monetization Integration
**Agent:** `sales-automator` or `fullstack-developer`

**Tasks:**
- Email capture setup
- Affiliate link integration
- Analytics configuration
- Payment processing

**Example:**
```javascript
Task tool → sales-automator → "Set up email capture with welcome sequence, integrate affiliate links throughout content"
```

---

### Phase 2E: Early User Testing
**Agent:** `ux-researcher` or `product-manager`

**Tasks:**
- Beta user recruitment
- Feedback collection
- User testing sessions
- Iteration planning

**Example:**
```javascript
Task tool → ux-researcher → "Design user testing protocol, create feedback survey, analyze results for PMF indicators"
```

---

### Phase 2F: Content Creation
**Agent:** `content-marketer` or `seo-specialist`

**Tasks:**
- Blog post writing
- Social media content
- Email sequences
- SEO optimization

**Example:**
```javascript
Task tool → content-marketer → "Write 10 SEO-optimized blog posts for [Brand Name] targeting [keywords]"
```

---

### Phase 3: Deployment
**Agent:** `deployment-engineer` or `platform-engineer`

**Tasks:**
- Vercel deployment
- Domain configuration
- SSL setup
- Performance optimization

**Example:**
```javascript
Task tool → deployment-engineer → "Deploy to Vercel, configure custom domain, set up SSL and environment variables"
```

---

### Phase 4: Launch & Marketing
**Agents:** `content-marketer`, `seo-specialist`, `sales-automator`

**Tasks:**
- Launch campaigns
- Social media strategy
- Email marketing
- SEO implementation

**Example:**
```javascript
Task tool → content-marketer → "Create 30-day launch content calendar with social posts, emails, and PR outreach"
```

---

## HOW TO USE AGENTS IN PARALLEL

### Sequential Approach (Default)
Complete one phase before moving to next:
```
Week 3: 2A (Technical Setup)
Week 4: 2B (Backend)
Week 5-6: 2C (Frontend)
Week 7: 2D (Monetization)
Week 8: 2E (Testing)
Week 9: 2F (Content)
```
**Total Time:** 7 weeks

### Parallel Approach (Recommended)
Launch multiple agents simultaneously:
```
Week 3: 2A (Technical Setup)
Week 4-6: Launch all three agents:
  - Agent 1: 2B (Backend) → python-pro
  - Agent 2: 2C (Frontend) → frontend-developer
  - Agent 3: 2D (Monetization) → sales-automator
Week 7: 2E (Testing) → ux-researcher
Week 8: 2F (Content) → content-marketer
```
**Total Time:** 6 weeks (saves 1 week!)

**With 3 brands:**
- Sequential: 21 weeks
- Parallel: 6 weeks (launches all 3 simultaneously!)

---

## AGENT COORDINATION

### Communication Between Agents
**Use shared documentation:**
```
[Brand Name]/Website/docs/
├── technical-spec.md (Backend reference for Frontend)
├── api-documentation.md (Backend → Frontend)
├── design-system.md (Frontend → Backend)
└── integration-guide.md (Coordination doc)
```

### Merge Strategy
After parallel development (Week 6):
1. Backend agent completes API
2. Frontend agent completes UI
3. Monetization agent completes integrations
4. **Week 7:** Integration testing (combine all work)
5. **Week 8:** User testing on integrated system

---

## MANDATORY AGENT USAGE CHECKLIST

Before starting ANY phase, ask:

### ✅ Planning Phase (0-1)
- [ ] Used `market-researcher` for market analysis?
- [ ] Used `business-analyst` for business model?
- [ ] Used `product-manager` for roadmap?
- [ ] Used `content-marketer` for content strategy?

### ✅ Build Phase (2)
- [ ] Used `platform-engineer` for setup (2A)?
- [ ] Used `python-pro` or `secure-backend-dev` for backend (2B)?
- [ ] Used `frontend-developer` for frontend (2C)?
- [ ] Used `sales-automator` for monetization (2D)?
- [ ] Used `ux-researcher` for user testing (2E)?
- [ ] Used `content-marketer` for content (2F)?

### ✅ Deploy Phase (3)
- [ ] Used `deployment-engineer` for deployment (3A)?
- [ ] Used `analytics-architect` for PMF tracking (3B)?
- [ ] Used `test-automator` for testing (3C)?

### ✅ Launch Phase (4)
- [ ] Used `content-marketer` for launch content?
- [ ] Used `seo-specialist` for SEO?
- [ ] Used `sales-automator` for email campaigns?

---

## WHEN NOT TO USE AGENTS

**Only do tasks directly when:**
1. Simple file reads/updates (using Read/Edit tools)
2. Quick status checks
3. Documentation updates
4. Answering user questions about the system

**For everything else → USE AGENTS!**

---

## AGENT ASSIGNMENT REFERENCE

| Phase | Sub-Phase | Primary Agent | Secondary Option | Parallel? |
|-------|-----------|---------------|------------------|-----------|
| 0 | Idea Validation | business-analyst | product-manager | No |
| 1A | Brand Identity | content-marketer | product-manager | No |
| 1B | Market Research | market-researcher | business-analyst | No |
| 1C | Product Planning | product-manager | fullstack-developer | No |
| 1D | AI Team Assembly | system-architecture-advisor | platform-engineer | No |
| **2A** | **Technical Setup** | **platform-engineer** | **fullstack-developer** | **No** |
| **2B** | **Backend Development** | **python-pro** | **secure-backend-dev** | **YES ║** |
| **2C** | **Frontend Development** | **frontend-developer** | **react-specialist** | **YES ║** |
| **2D** | **Monetization Integration** | **sales-automator** | **fullstack-developer** | **YES ║** |
| **2E** | **Early User Testing** | **ux-researcher** | **product-manager** | **No** |
| **2F** | **Content Creation** | **content-marketer** | **seo-specialist** | **No** |
| 3A | Production Deployment | deployment-engineer | platform-engineer | No |
| 3B | PMF Validation | analytics-architect | product-manager | No |
| 3C | Pre-Launch Testing | test-automator | fullstack-developer | No |
| 4A | Official Launch | content-marketer | sales-automator | No |
| 4B | Scale Preparation | platform-engineer | system-architecture-advisor | No |
| 4C | Optimization | seo-specialist | analytics-architect | No |

**║ = Can run in parallel**

---

## EXAMPLE PARALLEL WORKFLOW

### Week 4-6: Running 3 Agents in Parallel

**1. Launch Backend Agent (2B)**
```javascript
Task tool → python-pro → subagent_type: "python-pro"
Prompt: "Build complete backend for [Brand Name] including:
- Prisma schema for users, posts, comments
- NextAuth.js authentication
- API routes for CRUD operations
- Email service integration (Resend)
Document all APIs in docs/api-documentation.md"
```

**2. Launch Frontend Agent (2C)** (same message, parallel execution)
```javascript
Task tool → frontend-developer → subagent_type: "frontend-developer"
Prompt: "Build responsive frontend for [Brand Name] including:
- Component library with shadcn/ui
- Homepage, About, Blog, Contact pages
- Mobile-responsive design
- Reference docs/api-documentation.md for API integration"
```

**3. Launch Monetization Agent (2D)** (same message, parallel execution)
```javascript
Task tool → sales-automator → subagent_type: "sales-automator"
Prompt: "Set up monetization for [Brand Name] including:
- Email capture form and welcome sequence
- Google Analytics 4 integration
- Affiliate link tracking system
- Stripe payment integration (if needed)"
```

**All three agents work simultaneously for 2-3 weeks!**

---

## TRACKING AGENT WORK

Use TodoWrite to track all agent tasks:

```markdown
### Phase 2B: Backend Development (Agent: python-pro)
- [ ] Database schema designed
- [ ] Prisma migrations created
- [ ] Authentication implemented
- [ ] API routes built
- [ ] Email service integrated

### Phase 2C: Frontend Development (Agent: frontend-developer)
- [ ] Component library built
- [ ] Homepage completed
- [ ] About page completed
- [ ] Blog page completed
- [ ] Contact page completed
- [ ] Mobile responsive verified

### Phase 2D: Monetization (Agent: sales-automator)
- [ ] Email capture working
- [ ] Analytics tracking
- [ ] Affiliate links integrated
- [ ] Payment processing (if needed)
```

---

## COMMON MISTAKES TO AVOID

### ❌ Don't Do This:
```javascript
// Writing backend code directly without using agent
Edit tool → "Add authentication to API route"
```

### ✅ Do This Instead:
```javascript
// Use the specialized agent
Task tool → secure-backend-dev → "Implement secure authentication for API routes with JWT tokens and rate limiting"
```

### ❌ Don't Do This:
```javascript
// Building multiple features yourself
"Let me build the homepage, API, and email system..."
```

### ✅ Do This Instead:
```javascript
// Launch 3 agents in parallel (single message with 3 Task tool calls)
1. frontend-developer → Homepage
2. python-pro → API
3. sales-automator → Email system
```

---

## SUCCESS METRICS

**You're using agents correctly if:**
- ✅ Every major feature is built by a specialized agent
- ✅ Phase 2B, 2C, 2D run in parallel
- ✅ Agents coordinate via shared documentation
- ✅ TodoWrite tracks all agent tasks
- ✅ Work completes faster with higher quality

**Red flags that you're NOT using agents:**
- ❌ Manually writing large amounts of code
- ❌ Building features sequentially when they could be parallel
- ❌ Not using Task tool for specialized work
- ❌ Taking longer than timeline estimates

---

## SUMMARY

### The Rule (Never Forget This!)
**If a specialized agent exists for the task → USE IT!**

### The Process
1. Identify the phase/sub-phase
2. Check the agent assignment table
3. Launch the appropriate agent via Task tool
4. Coordinate multiple agents via shared docs
5. Track all work in TodoWrite

### The Benefit
- **Higher quality** (specialized expertise)
- **Faster development** (parallel execution)
- **Better outcomes** (best practices followed)
- **Scalable approach** (can build multiple brands simultaneously)

---

**This is not a suggestion - it's a system requirement!**

**Every session should heavily utilize agents through the Task tool.**

---

*Last Updated: November 2, 2025*
*Maintained by: Oliver Productions*
*Priority: CRITICAL - Must follow in all sessions*
