# TrapVerse Labs - Technical Architecture

## Tech Stack Overview

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Framework** | Next.js 14+ (App Router) | Full-stack React framework |
| **Styling** | Tailwind CSS | Utility-first CSS |
| **CMS** | Sanity.io | Blog, artist profiles, beat catalog |
| **Database** | PostgreSQL | User data, orders, subscriptions |
| **ORM** | Prisma | Type-safe database access |
| **Auth** | NextAuth.js | Authentication and sessions |
| **Email** | SendGrid | Transactional and marketing emails |
| **Payments** | Stripe | Beat sales, subscriptions, licensing |
| **Analytics** | GA4 + Mixpanel | Traffic and event tracking |
| **Hosting** | Vercel | Edge deployment, serverless functions |
| **Audio Storage** | AWS S3 / Cloudflare R2 | Beat file hosting and streaming |
| **CDN** | Vercel Edge / Cloudflare | Global asset delivery |

---

## Project Structure

```
Website/
├── prisma/
│   └── schema.prisma          # Database schema
├── public/
│   └── robots.txt             # SEO crawl directives
├── src/
│   ├── app/
│   │   ├── globals.css        # Global styles
│   │   ├── layout.tsx         # Root layout
│   │   ├── page.tsx           # Homepage
│   │   ├── about/page.tsx     # About page
│   │   ├── artists/page.tsx   # Artist roster
│   │   ├── beats/page.tsx     # Beat marketplace
│   │   ├── blog/
│   │   │   ├── page.tsx       # Blog listing
│   │   │   └── [slug]/page.tsx # Individual post
│   │   ├── contact/page.tsx   # Contact form
│   │   ├── privacy/page.tsx   # Privacy policy
│   │   ├── terms/page.tsx     # Terms of service
│   │   ├── affiliate-disclosure/page.tsx
│   │   ├── robots.ts          # Dynamic robots
│   │   ├── sitemap.ts         # Dynamic sitemap
│   │   ├── studio/[[...tool]]/page.tsx # Sanity Studio
│   │   └── api/
│   │       ├── newsletter/route.ts  # Email subscription
│   │       └── track/route.ts       # Affiliate tracking
│   ├── components/
│   │   └── layout/
│   │       ├── Header.tsx     # Site navigation
│   │       └── Footer.tsx     # Site footer
│   ├── lib/
│   │   ├── config/
│   │   │   └── brand.ts       # Brand configuration
│   │   └── db/
│   │       └── prisma.ts      # Prisma client singleton
│   └── middleware.ts          # Security headers, rate limiting
├── .env.example               # Environment template
├── .eslintrc.json             # Linting rules
├── .gitignore                 # Git ignore patterns
├── next.config.js             # Next.js configuration
├── package.json               # Dependencies
├── postcss.config.js          # PostCSS (Tailwind)
├── sanity.config.ts           # Sanity CMS config
├── tailwind.config.ts         # Tailwind theme
└── tsconfig.json              # TypeScript config
```

---

## Database Schema

### Core Models

- **User** - Email subscribers, registered users
- **Beat** - Beat catalog with metadata (title, BPM, key, price, audio URL)
- **Artist** - Artist profiles with bio, image, social links
- **Order** - Beat purchases and license records
- **AffiliateClick** - Tracking for affiliate link clicks
- **NewsletterSubscriber** - Email list management

### Key Relationships

- User -> Orders (one-to-many)
- Artist -> Beats (one-to-many)
- Beat -> Orders (one-to-many)

---

## API Routes

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/newsletter` | POST | Subscribe to email list |
| `/api/track` | POST | Track affiliate clicks |
| `/api/beats` | GET | Fetch beat catalog (future) |
| `/api/artists` | GET | Fetch artist roster (future) |
| `/api/orders` | POST | Process beat purchase (future) |
| `/api/auth/[...nextauth]` | ALL | Authentication (future) |

---

## Security Measures

### HTTP Headers (via middleware and next.config.js)
- Content-Security-Policy
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- X-XSS-Protection: 1; mode=block
- Referrer-Policy: strict-origin-when-cross-origin
- Strict-Transport-Security (HSTS)
- Permissions-Policy

### Application Security
- CSRF protection on all forms
- Input validation with Zod schemas
- Rate limiting on API routes
- SQL injection prevention via Prisma parameterized queries
- XSS prevention via React auto-escaping and CSP
- Environment variable protection (no client exposure)

---

## Deployment

### Vercel Configuration
- Production branch: `main`
- Preview branches: `dev`, `staging`
- Environment variables set in Vercel dashboard
- Automatic deployments on push

### Environment Variables Required

```
DATABASE_URL=             # PostgreSQL connection string
NEXTAUTH_URL=             # Site URL for auth
NEXTAUTH_SECRET=          # Auth encryption key
SANITY_PROJECT_ID=        # Sanity project ID
SANITY_DATASET=           # Sanity dataset (production)
SANITY_API_TOKEN=         # Sanity API write token
SENDGRID_API_KEY=         # SendGrid email API key
STRIPE_SECRET_KEY=        # Stripe payment key
STRIPE_PUBLISHABLE_KEY=   # Stripe public key
GA_MEASUREMENT_ID=        # Google Analytics ID
AWS_ACCESS_KEY_ID=        # S3 for audio files
AWS_SECRET_ACCESS_KEY=    # S3 secret
AWS_S3_BUCKET=            # S3 bucket name
```

---

## Performance Targets

| Metric | Target |
|--------|--------|
| Lighthouse Performance | 90+ |
| Lighthouse Accessibility | 95+ |
| First Contentful Paint | < 1.5s |
| Largest Contentful Paint | < 2.5s |
| Cumulative Layout Shift | < 0.1 |
| Time to Interactive | < 3.5s |
| Page Size (compressed) | < 500KB |

---

## Future Technical Roadmap

### Phase 2 - Beat Marketplace
- Stripe payment integration
- Audio preview player component
- Beat upload and management dashboard
- License agreement generation

### Phase 3 - Artist Portal
- Artist dashboard with analytics
- Release management system
- Distribution API integrations (DistroKid, Spotify)
- Revenue tracking and payout system

### Phase 4 - AI Integration
- AI beat generation API
- Style transfer and remix tools
- Automated mixing and mastering pipeline
- AI-powered A&R recommendations
