# Universal Technical Architecture Guide
*Standardized Technical Implementation for All SMG Side Brands*
*Last Updated: October 2025*

---

## 🎯 OVERVIEW

This document outlines the standardized technical architecture, frameworks, and database design for all SMG side brand platforms. This serves as the definitive reference for all technical development decisions and ensures consistency across the entire brand portfolio.

---

## 🏗️ CORE TECHNOLOGY STACK

### Frontend Framework
- **Next.js 14** with App Router
  - Server Components for optimal performance
  - Static Site Generation (SSG) with Incremental Static Regeneration (ISR)
  - Built-in image optimization
  - TypeScript for type safety
  - SEO-friendly with automatic sitemap generation

### Styling & UI
- **Tailwind CSS 3.4** for utility-first styling
- **Responsive Design** with mobile-first approach
- **Radix UI** for accessible component primitives
- **Framer Motion** for smooth animations
- **Custom Component Library** built with React components

### Database & Storage
- **SQLite** for application data (lightweight, serverless, perfect for starting)
  - Zero configuration required
  - File-based database (prisma/dev.db)
  - Easy backup and migration
  - Perfect for sites under 100k visitors/month

- **Migration Path to PostgreSQL** when scaling:
  - Switch when reaching 100k+ monthly visitors
  - Same Prisma schema, minimal code changes
  - Options: Supabase (free tier), Railway, or Vercel Postgres

- **Sanity CMS** for content management
  - 10k documents free
  - 100k API calls/month free
  - Media asset storage
  - Real-time collaboration
  - Blog posts, product reviews, guides

- **YouTube** for video hosting
  - Free unlimited hosting
  - Built-in CDN
  - Automatic transcoding
  - Embed on website

### Authentication & Security (Optional - for member sites)
- **NextAuth.js** for user authentication and session management
- **HTTPS enforcement** across all applications
- **Content Security Policy** headers
- **Input sanitization** for user-generated content
- **Rate limiting** on API endpoints

### Payment Processing (For E-commerce Brands)
- **Stripe** for secure payment processing
  - One-time payments
  - Subscription management
  - 2.9% + 30¢ per transaction
- **Shopify Lite** alternative ($9/month)
  - Buy buttons for simple stores
  - Shopping cart integration

### Email Marketing
- **SendGrid** for transactional emails
  - Free tier: 100 emails/day
  - Scalable to 100k/day
  - Template system
  - Analytics tracking

- **ConvertKit** for marketing automation
  - Newsletter management
  - Lead magnet delivery
  - Automated sequences
  - Subscriber management

### Real-time Features (Advanced - Optional)
- **WebSocket** connections for live community features
- **Discord API** for community integration
- **Pusher** for real-time notifications
- **In-memory caching** for frequently accessed data

---

## 📁 UNIVERSAL APPLICATION ARCHITECTURE

### Monorepo Structure
```
brand-name/
├── prisma/
│   ├── schema.prisma          # Database schema
│   ├── dev.db                 # SQLite database file
│   ├── migrations/            # Version-controlled schema changes
│   └── seed.ts               # Sample data for development
├── src/
│   ├── app/                   # Next.js App Router
│   │   ├── (marketing)/      # Public pages
│   │   │   ├── page.tsx      # Homepage
│   │   │   ├── about/page.tsx
│   │   │   ├── blog/
│   │   │   │   ├── page.tsx
│   │   │   │   └── [slug]/page.tsx
│   │   │   └── contact/page.tsx
│   │   ├── (shop)/           # E-commerce section
│   │   │   ├── products/page.tsx
│   │   │   ├── cart/page.tsx
│   │   │   └── checkout/page.tsx
│   │   ├── (legal)/          # Legal pages
│   │   │   ├── privacy/page.tsx
│   │   │   ├── terms/page.tsx
│   │   │   └── affiliate-disclosure/page.tsx
│   │   ├── api/              # API routes
│   │   │   ├── newsletter/route.ts
│   │   │   ├── track/route.ts
│   │   │   ├── webhooks/
│   │   │   └── contact/route.ts
│   │   ├── layout.tsx        # Root layout
│   │   ├── not-found.tsx     # 404 page
│   │   ├── error.tsx         # Error page
│   │   └── globals.css       # Global styles
│   ├── components/           # React components
│   │   ├── ui/              # Base components
│   │   │   ├── button.tsx
│   │   │   ├── card.tsx
│   │   │   ├── input.tsx
│   │   │   ├── modal.tsx
│   │   │   └── ...
│   │   ├── layout/          # Layout components
│   │   │   ├── header.tsx
│   │   │   ├── footer.tsx
│   │   │   ├── navigation.tsx
│   │   │   └── sidebar.tsx
│   │   ├── features/        # Feature-specific components
│   │   │   ├── newsletter-form.tsx
│   │   │   ├── product-card.tsx
│   │   │   ├── blog-post-card.tsx
│   │   │   └── affiliate-link.tsx
│   │   └── analytics/       # Tracking components
│   │       ├── google-analytics.tsx
│   │       └── tracking.tsx
│   ├── lib/                 # Utility libraries
│   │   ├── config/
│   │   │   └── brand.ts     # Brand-specific config
│   │   ├── db/
│   │   │   ├── prisma.ts    # Prisma client
│   │   │   └── queries.ts   # Common queries
│   │   ├── email/
│   │   │   ├── sendgrid.ts  # Email service
│   │   │   └── templates.ts # Email templates
│   │   ├── cms/
│   │   │   └── sanity.ts    # Sanity client
│   │   ├── utils/
│   │   │   ├── formatters.ts
│   │   │   └── validators.ts
│   │   └── hooks/
│   │       ├── useAnalytics.ts
│   │       └── useNewsletter.ts
│   ├── styles/
│   │   └── globals.css      # Global styles
│   └── types/
│       └── index.ts         # TypeScript definitions
├── public/
│   ├── images/              # Static images
│   ├── fonts/               # Custom fonts
│   ├── favicon.ico
│   ├── robots.txt
│   └── sitemap.xml
├── .env.local               # Environment variables
├── .env.example             # Example env file
├── .gitignore              # Git ignore file
├── next.config.js          # Next.js configuration
├── tailwind.config.ts      # Tailwind configuration
├── tsconfig.json           # TypeScript configuration
├── package.json            # Dependencies
└── README.md               # Project documentation
```

---

## 🗄️ UNIVERSAL DATABASE SCHEMA

### Core Models (Prisma Schema)

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"  // Change to "postgresql" when scaling
  url      = env("DATABASE_URL")
}

// User/Subscriber Model
model User {
  id            String    @id @default(uuid())
  email         String    @unique
  name          String?
  role          String    @default("subscriber")
  subscribed    Boolean   @default(true)
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt

  orders        Order[]
  clicks        Click[]
}

// Product/Affiliate Product Model
model Product {
  id              String    @id @default(uuid())
  name            String
  slug            String    @unique
  description     String?
  price           Float?
  comparePrice    Float?
  affiliateUrl    String?
  commissionRate  Float?
  category        String?
  tags            String?   // JSON string for SQLite
  featured        Boolean   @default(false)
  active          Boolean   @default(true)
  imageUrl        String?
  clicks          Int       @default(0)
  conversions     Int       @default(0)
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt

  orderItems      OrderItem[]
  clickTracking   Click[]
}

// Blog Post Model
model BlogPost {
  id            String    @id @default(uuid())
  title         String
  slug          String    @unique
  content       String    // Rich text as JSON
  excerpt       String?
  author        String?
  category      String?
  tags          String?   // JSON string
  featured      Boolean   @default(false)
  published     Boolean   @default(false)
  publishedAt   DateTime?
  viewCount     Int       @default(0)
  imageUrl      String?
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

// Order Model (E-commerce)
model Order {
  id            String    @id @default(uuid())
  userId        String?
  email         String
  total         Float
  status        String    @default("pending")
  paymentId     String?
  items         String    // JSON string of items
  createdAt     DateTime  @default(now())

  user          User?     @relation(fields: [userId], references: [id])
  orderItems    OrderItem[]
}

// Order Items
model OrderItem {
  id            String    @id @default(uuid())
  orderId       String
  productId     String
  quantity      Int
  price         Float

  order         Order     @relation(fields: [orderId], references: [id])
  product       Product   @relation(fields: [productId], references: [id])
}

// Click Tracking (Affiliate Analytics)
model Click {
  id            String    @id @default(uuid())
  productId     String?
  userId        String?
  url           String
  referrer      String?
  userAgent     String?
  ip            String?
  converted     Boolean   @default(false)
  createdAt     DateTime  @default(now())

  product       Product?  @relation(fields: [productId], references: [id])
  user          User?     @relation(fields: [userId], references: [id])
}

// Newsletter Management
model Newsletter {
  id            String    @id @default(uuid())
  subject       String
  content       String    // HTML content
  sentAt        DateTime?
  recipients    Int       @default(0)
  opens         Int       @default(0)
  clicks        Int       @default(0)
  createdAt     DateTime  @default(now())
}

// Lead Magnet Downloads
model LeadMagnet {
  id            String    @id @default(uuid())
  email         String
  name          String?
  magnetType    String    // "checklist", "guide", "template"
  downloadCount Int       @default(0)
  createdAt     DateTime  @default(now())
}
```

---

## 🔐 SECURITY & PERFORMANCE

### Security Measures
- **Authentication**: NextAuth.js with secure session management (if needed)
- **Authorization**: Role-based access control (RBAC) for admin features
- **Data Protection**: HTTPS enforcement, CSP headers, CORS configuration
- **Payment Security**: PCI DSS compliance via Stripe (never store card data)
- **Input Validation**: Zod schemas for all form inputs
- **Rate Limiting**: API endpoint protection against abuse
- **SQL Injection Prevention**: Prisma ORM with parameterized queries
- **XSS Prevention**: React's built-in escaping + CSP headers

### Performance Optimization
- **Caching Strategy**:
  - Static pages cached at CDN edge
  - ISR for blog posts (revalidate every 60 seconds)
  - In-memory cache for frequently accessed data

- **Image Optimization**:
  - Next.js Image component (automatic WebP/AVIF)
  - Lazy loading below the fold
  - Responsive images with srcset
  - CDN delivery via Vercel Edge Network

- **Code Splitting**:
  - Automatic route-based splitting
  - Dynamic imports for heavy components
  - Separate vendor bundles

- **Database Optimization**:
  - Indexed queries on frequently searched fields
  - Connection pooling
  - Query result caching
  - Pagination for large datasets

### Monitoring & Analytics
- **Error Tracking**: Console logging + optional Sentry integration
- **Performance Monitoring**: Core Web Vitals tracking (LCP, FID, CLS)
- **User Analytics**: Google Analytics 4 (privacy-compliant)
- **Business Metrics**: Custom dashboards for revenue, conversions, traffic
- **Uptime Monitoring**: Vercel Analytics + optional UptimeRobot

---

## 🚀 DEPLOYMENT & HOSTING

### Deployment Strategy
- **Hosting Platform**: Vercel (recommended)
  - Free tier: 100GB bandwidth, unlimited sites
  - Automatic HTTPS
  - Edge network CDN
  - Zero-config deployment
  - Preview deployments for PRs

- **Database Hosting**:
  - SQLite: Stored in repository (dev.db)
  - PostgreSQL: Supabase free tier or Vercel Postgres
  - Automated backups daily

- **CMS Hosting**: Sanity Studio
  - Deploy to sanity-studio.yourbrand.com
  - Or self-host on Vercel

- **CI/CD**: GitHub Actions
  - Automated testing on PR
  - Automatic deployment to Vercel
  - Lighthouse performance checks

### Domain Configuration
- **Domain Registrar**: Hostinger ($2.99/month includes email)
- **DNS Setup**: Point to Vercel nameservers
- **Email Setup**: Hostinger email forwarding
  - info@yourbrand.com
  - support@yourbrand.com
  - newsletter@yourbrand.com

### Testing Strategy
- **Unit Tests**: Jest + React Testing Library
- **Integration Tests**: Playwright for E2E testing
- **Performance Tests**: Lighthouse CI in GitHub Actions
- **Security Tests**: OWASP dependency scanning
- **Load Testing**: Optional k6 for high-traffic sites

---

## 🔧 DEVELOPMENT WORKFLOW

### Local Development Setup

```bash
# 1. Clone repository
git clone https://github.com/your-org/brand-name.git
cd brand-name

# 2. Install dependencies
npm install

# 3. Set up environment variables
cp .env.example .env.local
# Edit .env.local with your credentials

# 4. Initialize database
npx prisma migrate dev --name init
npx prisma generate

# 5. (Optional) Seed database
npx prisma db seed

# 6. Start development server
npm run dev
```

### Git Workflow
```bash
# Feature branch workflow
git checkout -b feature/new-blog-section
# Make changes...
git add .
git commit -m "Add new blog section with featured posts"
git push origin feature/new-blog-section
# Create PR in GitHub
```

### Environment Variables Template

```env
# .env.local - Complete template

# ===== BRAND CONFIGURATION =====
NEXT_PUBLIC_BRAND_NAME="Your Brand Name"
NEXT_PUBLIC_SITE_URL=https://yourdomain.com
NEXT_PUBLIC_SITE_DESCRIPTION="Your brand description"

# ===== DATABASE =====
DATABASE_URL="file:./prisma/dev.db"
# For PostgreSQL: DATABASE_URL="postgresql://user:pass@host:5432/dbname"

# ===== SANITY CMS =====
NEXT_PUBLIC_SANITY_PROJECT_ID=your_project_id
NEXT_PUBLIC_SANITY_DATASET=production
SANITY_API_TOKEN=your_token_for_mutations

# ===== SENDGRID EMAIL =====
SENDGRID_API_KEY=SG.xxxxxxxxxxxxxxxxxxxx
SENDGRID_FROM_EMAIL=info@yourdomain.com
SENDGRID_FROM_NAME="Your Brand"
SENDGRID_LIST_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

# ===== SHOPIFY (Optional - E-commerce) =====
NEXT_PUBLIC_SHOPIFY_DOMAIN=your-store.myshopify.com
NEXT_PUBLIC_SHOPIFY_STOREFRONT_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxxxx

# ===== ANALYTICS =====
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
NEXT_PUBLIC_PLAUSIBLE_DOMAIN=yourdomain.com

# ===== AFFILIATE PROGRAMS =====
AFFILIATE_SHAREASALE_ID=xxxxx
AFFILIATE_CJ_ID=xxxxx
AFFILIATE_AMAZON_TAG=yourtag-20

# ===== STRIPE (Optional - Payments) =====
STRIPE_SECRET_KEY=sk_test_xxxxxxxxxxxxxxxxxxxxxxxxxx
NEXT_PUBLIC_STRIPE_PUBLIC_KEY=pk_test_xxxxxxxxxxxxxxxxxxxxxxxxxx

# ===== AUTHENTICATION (Optional - NextAuth) =====
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-secret-here-generate-with-openssl
```

---

## 📈 SCALING CONSIDERATIONS

### When to Migrate from SQLite to PostgreSQL
**Switch when you reach any of these:**
- 100,000+ monthly visitors
- 10,000+ database records
- Multiple concurrent writes
- Need for advanced queries
- Distributed team collaboration

### Horizontal Scaling Options
**Database Layer**
- PostgreSQL with connection pooling (PgBouncer)
- Read replicas for query distribution
- Database sharding by brand (if multi-tenant)

**Application Layer**
- Serverless functions scale automatically on Vercel
- Edge functions for geographically distributed users
- Redis for session storage and caching

**File Storage & CDN**
- Cloudflare R2 for media assets
- Cloudflare CDN for global distribution
- Image optimization service (Cloudinary/Imgix)

### Feature Expansion Paths
**Mobile Apps**: React Native for iOS/Android
**Advanced Analytics**: Mixpanel or Amplitude integration
**Real-time Features**: WebSocket scaling with Socket.io or Pusher
**API Platform**: GraphQL for unified data access
**Multi-language**: i18n support for international markets

---

## ✅ IMPLEMENTATION CHECKLIST

### For Each New Brand
- [ ] Create Next.js project with TypeScript + Tailwind
- [ ] Set up Git repository
- [ ] Configure Prisma with SQLite
- [ ] Create Sanity project
- [ ] Set up SendGrid account
- [ ] Configure environment variables
- [ ] Implement core page templates
- [ ] Set up blog structure
- [ ] Configure analytics (Google Analytics)
- [ ] Add affiliate disclosure pages
- [ ] Test email flows
- [ ] Optimize for mobile
- [ ] Run Lighthouse audit (95+ score goal)
- [ ] Deploy to Vercel
- [ ] Configure custom domain
- [ ] Submit sitemap to Google Search Console
- [ ] Set up monitoring

---

## 🎨 BRAND-SPECIFIC CUSTOMIZATION

### What Changes Per Brand
- Color scheme (Tailwind config)
- Logo and brand assets
- Content (blog posts, products)
- Sanity schemas (customize content types)
- Email templates (SendGrid)
- Social media links
- SEO metadata

### What Stays Universal
- Technology stack
- Database schema structure
- Component architecture
- Security implementation
- Performance optimizations
- Deployment process
- Analytics setup
- API structure

---

## 📚 REFERENCE DOCUMENTATION

### Official Documentation
- Next.js: https://nextjs.org/docs
- Tailwind CSS: https://tailwindcss.com/docs
- Prisma: https://www.prisma.io/docs
- Sanity: https://www.sanity.io/docs
- SendGrid: https://docs.sendgrid.com/
- Vercel: https://vercel.com/docs

### Community Resources
- Next.js Discord: https://discord.gg/nextjs
- Prisma Slack: https://slack.prisma.io/
- Tailwind Discord: https://discord.gg/tailwindcss

---

*This universal technical architecture ensures consistency, maintainability, and scalability across all SMG side brands while allowing for brand-specific customization where needed.*

**Version**: 2.0
**Last Updated**: October 2025
**Maintainer**: SMG Technical Team
