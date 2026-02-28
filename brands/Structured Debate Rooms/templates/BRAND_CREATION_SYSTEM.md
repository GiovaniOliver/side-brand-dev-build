# Brand Creation System for Claude Code

**Version**: 1.0
**Last Updated**: October 2025
**Purpose**: Instructions for Claude Code to build SMG side brands using this template system

---

## 🎯 OVERVIEW

This system contains everything needed to build production-ready brand websites. When a user asks to create a new brand, follow this exact workflow using the templates and configurations in this folder.

---

## 📚 TEMPLATE HIERARCHY & USAGE

### Master Templates (Use in Order)

1. **Build_Brand_Template_Layout.md**
   - **Purpose**: Brand identity and strategic foundation
   - **When**: Start here for ALL new brands
   - **Outputs**: Brand identity document, audience personas, UVP
   - **Time**: 2-4 hours of strategic work

2. **TECHNICAL_ARCHITECTURE.md + UNIVERSAL_TECH_STACK_GUIDE.md**
   - **Purpose**: Complete technical implementation guide
   - **When**: After brand identity is defined
   - **Outputs**: Next.js project with full stack
   - **Time**: 1-2 hours automated setup

3. **MONETIZATION_GUIDE.md**
   - **Purpose**: Revenue generation framework
   - **When**: After technical setup is complete
   - **Outputs**: Affiliate accounts, product catalog, revenue streams
   - **Time**: 4-8 hours initial setup

4. **Startup_Marketing_Plan_System.md**
   - **Purpose**: 12-week marketing launch plan
   - **When**: Before going live
   - **Outputs**: Content calendar, ad campaigns, KPI tracking
   - **Time**: Ongoing execution

---

## 🤖 CLAUDE CODE WORKFLOW

### Step 1: Gather Brand Requirements

When user requests a new brand, ask:

```
1. Brand name and domain preference?
2. Target audience (who are we serving)?
3. Primary niche/industry?
4. Monetization focus (affiliate, ecommerce, SaaS)?
5. Primary brand color?
```

### Step 2: Run Setup Script

Execute the automated setup:

```bash
cd "../"  # Navigate to Side Brands parent folder
bash "1. Brand System Template & Tech Stack Template/setup-brand-website.sh"
```

**Script will prompt for**:
- Brand name (kebab-case)
- Brand display name
- Domain name
- Primary color hex

**Script creates**:
- Next.js 14 project
- Database schema (Prisma + SQLite)
- Folder structure
- Environment template
- Configuration files

### Step 3: Customize Brand Configuration

Edit the generated `lib/config/brand.ts`:

```typescript
export const brandConfig = {
  name: 'Brand Display Name',
  domain: 'brandomain.com',
  niche: 'specific industry/niche',
  colors: {
    primary: '#HEXCODE',
    secondary: '#HEXCODE',
    accent: '#HEXCODE',
  },
  email: {
    info: 'info@domain.com',
    support: 'support@domain.com',
    newsletter: 'newsletter@domain.com',
  },
  social: {
    twitter: '@handle',
    instagram: '@handle',
    facebook: 'pagename',
  },
  monetization: {
    primaryModel: 'affiliate' | 'ecommerce' | 'saas',
    affiliateNetworks: ['shareasale', 'cj', 'amazon'],
  }
}
```

### Step 4: Set Up External Services

**Required Services** (reference TECHNICAL_ARCHITECTURE.md):

1. **Sanity CMS** (if content-heavy brand)
   ```bash
   cd [brand-folder]
   npx sanity init
   # Project name: [brand-name]-blog
   # Dataset: production
   # Schema: Blog
   ```

2. **SendGrid Email**
   - Sign up at sendgrid.com
   - Get API key
   - Add to `.env.local`

3. **Vercel Hosting**
   ```bash
   vercel login
   vercel
   # Follow prompts
   ```

4. **Hostinger Domain**
   - Purchase domain
   - Point DNS to Vercel
   - Configure email forwarding

### Step 5: Implement Monetization (reference MONETIZATION_GUIDE.md)

**For Affiliate Brands**:
1. Join affiliate networks from AFFILIATE_PROGRAM_SIGNUP_GUIDE.md
2. Create product database using Prisma Product model
3. Implement affiliate click tracking

**For E-commerce Brands**:
1. Set up Shopify Lite ($9/mo)
2. Configure Shopify API keys
3. Implement cart and checkout

**For SaaS Brands**:
1. Implement Stripe payment integration
2. Set up user authentication (NextAuth)
3. Configure database schema for users/subscriptions

### Step 6: Create Initial Content

Using Sanity CMS or markdown:
1. Homepage content
2. About page
3. 10 initial blog posts (use niche from brandConfig)
4. Privacy policy, Terms, Affiliate disclosure

### Step 7: Deploy

```bash
cd [brand-folder]
npm run build  # Test build
vercel --prod   # Deploy to production
```

**Post-Deployment**:
1. Configure domain in Vercel
2. Set environment variables in Vercel dashboard
3. Test all functionality
4. Submit sitemap to Google Search Console

---

## 📋 FOLDER STRUCTURE (Created Automatically)

```
[Brand Name]/
├── website/                        # All Next.js code in this folder
│   ├── app/
│   │   ├── (marketing)/
│   │   │   ├── page.tsx              # Homepage
│   │   │   ├── about/page.tsx
│   │   │   ├── blog/
│   │   │   │   ├── page.tsx          # Blog index
│   │   │   │   └── [slug]/page.tsx   # Blog post
│   │   │   └── contact/page.tsx
│   │   ├── (shop)/                    # E-commerce only
│   │   │   ├── products/page.tsx
│   │   │   ├── cart/page.tsx
│   │   │   └── checkout/page.tsx
│   │   ├── (legal)/
│   │   │   ├── privacy/page.tsx
│   │   │   ├── terms/page.tsx
│   │   │   └── affiliate-disclosure/page.tsx
│   │   ├── api/
│   │   │   ├── newsletter/route.ts
│   │   │   ├── track/route.ts         # Affiliate click tracking
│   │   │   └── webhooks/
│   │   ├── layout.tsx
│   │   └── globals.css
│   ├── components/
│   │   ├── ui/                         # Base UI components
│   │   ├── layout/                     # Header, Footer, Nav
│   │   ├── features/                   # Feature-specific components
│   │   └── analytics/                  # Tracking components
│   ├── lib/
│   │   ├── config/brand.ts             # Brand configuration
│   │   ├── db/prisma.ts                # Database client
│   │   ├── email/sendgrid.ts           # Email service
│   │   ├── cms/sanity.ts               # CMS client (if used)
│   │   ├── utils/                      # Utility functions
│   │   └── hooks/                      # React hooks
│   ├── prisma/
│   │   ├── schema.prisma               # Database schema
│   │   └── dev.db                      # SQLite database
│   ├── public/
│   │   ├── images/
│   │   └── robots.txt
│   ├── .env.local                      # Environment variables
│   ├── next.config.js
│   ├── tailwind.config.ts
│   ├── package.json
│   └── README.md
├── docs/                           # Brand-specific documentation (optional)
│   ├── market-research.md
│   ├── brand-guide.md
│   └── content-plan.md
└── README.md                       # Brand overview
```

---

## 🗄️ DATABASE MODELS (Universal Schema)

All brands use this Prisma schema (from TECHNICAL_ARCHITECTURE.md):

```prisma
// Key models (see TECHNICAL_ARCHITECTURE.md for complete schema)
- User          # Subscribers and customers
- Product       # Affiliate or physical products
- BlogPost      # Content posts
- Order         # E-commerce orders
- Click         # Affiliate click tracking
- Newsletter    # Email campaigns
- LeadMagnet    # Download tracking
```

**Migration Commands**:
```bash
npx prisma generate    # Generate Prisma client
npx prisma migrate dev # Create migration
npx prisma studio      # View database
```

---

## 🎨 CUSTOMIZATION PER BRAND

### What Changes Per Brand:
- Brand colors (tailwind.config.ts)
- Logo and assets
- Content (blog posts, products)
- Sanity schemas (if using CMS)
- Email templates
- Social media links
- SEO metadata

### What Stays Universal:
- Technology stack (Next.js, Prisma, Tailwind)
- Database schema structure
- Component architecture
- Security implementation
- Performance optimizations
- Deployment process

---

## ✅ PRE-LAUNCH CHECKLIST

Before marking brand as complete, verify:

- [ ] All environment variables configured
- [ ] Database migrated and seeded
- [ ] Domain connected and SSL active
- [ ] Email service working (test send)
- [ ] Analytics installed (Google Analytics)
- [ ] Legal pages published (Privacy, Terms, Disclosure)
- [ ] 10+ blog posts published
- [ ] Social media accounts created
- [ ] Affiliate programs joined (5+ networks)
- [ ] Build successful (`npm run build`)
- [ ] Deployed to Vercel
- [ ] Lighthouse score 90+ (run audit)
- [ ] Mobile responsive (test on devices)
- [ ] SEO metadata complete
- [ ] Sitemap submitted to Google

---

## 🔧 COMMON TASKS

### Add New Blog Post
```bash
# In Sanity Studio (if using CMS)
# Or create markdown file in app/blog/posts/

# Then regenerate static pages
npm run build
```

### Add Affiliate Product
```typescript
// Use Prisma to add to database
await prisma.product.create({
  data: {
    name: 'Product Name',
    slug: 'product-slug',
    affiliateUrl: 'https://affiliate.link',
    commissionRate: 15.0,
    category: 'category',
    featured: true,
  }
})
```

### Update Brand Colors
```typescript
// Edit tailwind.config.ts
colors: {
  primary: {
    500: '#NEW_HEX', // Update main color
  }
}

// Then rebuild
npm run build
```

---

## 🚨 TROUBLESHOOTING

### Build Fails
- Check all environment variables are set
- Verify database connection
- Run `npx prisma generate`
- Check for TypeScript errors

### Domain Not Working
- Verify DNS records in Hostinger
- Check Vercel domain configuration
- Wait 24-48 hours for DNS propagation

### Email Not Sending
- Verify SendGrid API key
- Check email quota (100/day free)
- Test with simple send first

### Database Errors
- Run `npx prisma migrate reset` (dev only)
- Check DATABASE_URL in .env.local
- Verify schema matches code

---

## 📖 REFERENCE DOCUMENTS

**For Strategic Planning**:
- `Build_Brand_Template_Layout.md` - Brand identity
- `Startup_Marketing_Plan_System.md` - Marketing strategy

**For Technical Implementation**:
- `TECHNICAL_ARCHITECTURE.md` - System architecture
- `UNIVERSAL_TECH_STACK_GUIDE.md` - Complete tech guide
- `setup-brand-website.sh` - Automated setup script

**For Monetization**:
- `MONETIZATION_GUIDE.md` - Complete revenue framework
- `AFFILIATE_PROGRAM_SIGNUP_GUIDE.md` - Affiliate programs list

**For Quick Launch**:
- `PRIORITY_BRANDS_QUICK_START.md` - Fast implementation examples

**For Operations**:
- `Startup_Brand_SOP_Template.md` - Operational procedures

---

## 🎯 SUCCESS METRICS

**Month 1 Targets**:
- Website live and functional
- 10+ blog posts published
- 5+ affiliate programs joined
- 500+ email subscribers
- Google Analytics configured
- $100-500 revenue

**Month 3 Targets**:
- 30+ blog posts
- 2,000+ email subscribers
- 10+ affiliate products promoted
- Paid ads running
- $1,000-2,000 revenue

**Month 6 Targets**:
- 60+ blog posts
- 5,000+ email subscribers
- Multiple revenue streams active
- $3,000-5,000 revenue

---

## 💡 TIPS FOR CLAUDE CODE

1. **Always start with setup script** - Don't manually create structure
2. **Use exact schema from TECHNICAL_ARCHITECTURE.md** - Don't modify
3. **Reference templates** - Don't invent new patterns
4. **Follow folder structure exactly** - Consistency is critical
5. **Test build before deploying** - Catch errors early
6. **Set up analytics first** - Track from day 1
7. **Create .env.example** - Document required variables
8. **Add README to each brand** - Specific setup notes

---

## 🔄 MAINTENANCE

**Weekly**:
- Publish 2-3 blog posts
- Check affiliate program performance
- Review analytics
- Respond to emails

**Monthly**:
- Update dependencies (`npm update`)
- Review and optimize top content
- Analyze revenue by source
- Adjust marketing spend

**Quarterly**:
- Major feature additions
- Design refresh (if needed)
- SEO audit
- Performance optimization

---

**This system is production-ready. Follow it exactly for consistent, scalable brand creation.**

*For questions or clarifications, reference the specific template documents listed above.*
