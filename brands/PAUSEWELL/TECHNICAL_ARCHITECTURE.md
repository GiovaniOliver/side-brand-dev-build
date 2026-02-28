# PAUSEWELL - Technical Architecture

## Technology Stack & Infrastructure

**Last Updated:** November 2, 2025
**Status:** Planning Phase
**Target Launch:** Q1 2026

---

## 🎯 Technical Overview

PAUSEWELL follows the standard Side-Brands tech stack with specific additions for:
- Course/membership platform integration
- E-commerce for supplements
- Community platform
- Email marketing automation
- Payment processing for multiple revenue streams

---

## 🏗️ Core Technology Stack

### Frontend

**Framework:** Next.js 14+ (App Router)
- Server-side rendering for SEO
- Dynamic routing for course content
- Static generation for blog/marketing pages
- API routes for backend logic

**Language:** TypeScript
- Type safety across all components
- Better IDE support and error catching
- Improved code maintainability

**Styling:** Tailwind CSS
- Utility-first CSS framework
- Responsive design built-in
- Custom color palette (Plum, Terracotta, Sage)
- Component-based styling

**UI Components:** Radix UI + shadcn/ui
- Accessible components out of the box
- Customizable to brand aesthetic
- Button, Form, Dialog, Toast components
- Modal for course lessons

---

### Backend

**Database:** SQLite (Development) → PostgreSQL (Production)
- SQLite for local development
- PostgreSQL on Vercel for production
- Easy migration path

**ORM:** Prisma
- Type-safe database queries
- Schema migrations
- Database modeling for:
  - Users
  - Course progress
  - Memberships
  - Orders
  - Products
  - Community posts

**API:** Next.js API Routes
- RESTful endpoints
- Authentication middleware
- Payment webhooks
- Email integrations

---

### Content Management

**CMS:** Sanity.io (for Blog & Educational Content)
- Headless CMS for blog posts
- Educational content library
- Recipe database
- Workout library
- Admin dashboard for content team

**Course Platform Integration Options:**

**Option 1: Custom-Built (Recommended)**
- Full control over experience
- Integrated with main site
- Custom progress tracking
- Use Prisma for course data
- Video hosting via Mux or Vimeo

**Option 2: Teachable/Kajabi Integration**
- Faster to launch
- Built-in features (drip content, certificates)
- Monthly cost: $39-$119
- Less customization

**Decision:** Start with Teachable for speed, migrate to custom once validated

---

### Payment Processing

**Primary:** Stripe
- Subscription management (membership)
- One-time payments (course)
- Recurring billing (supplements)
- Invoicing (corporate wellness)
- Strong fraud protection

**Features to Implement:**
- Customer portal for subscription management
- Multiple pricing tiers
- Coupon/promo codes
- Failed payment recovery emails
- Revenue analytics

---

### Email Marketing

**Provider:** SendGrid or ConvertKit

**SendGrid (Transactional + Marketing)**
- Free tier: 100 emails/day
- Paid: $19.95/month for 50K emails
- API integration
- Template system
- Analytics

**ConvertKit (Recommended for PAUSEWELL)**
- Built for creators
- Visual automation builder
- Tagging and segmentation
- Landing pages included
- Free up to 1,000 subscribers
- $29/month for up to 3,000

**Email Sequences Needed:**
- Welcome sequence (7 emails)
- Course onboarding (5 emails)
- Membership nurture (monthly)
- Cart abandonment recovery
- Win-back campaigns
- Product launch sequences

---

### Community Platform

**Options:**

**Option 1: Facebook Group (Phase 1 - Free)**
- Zero cost
- Members already familiar
- Easy to moderate
- Good for initial validation
- Migrate later if needed

**Option 2: Circle (Long-term)**
- White-label community
- Integrated with website
- Better control and branding
- $89-$219/month
- More professional

**Option 3: Custom-Built (Future)**
- Full control
- Best user experience
- Requires development time
- Phase 3+ consideration

**Decision:** Start with Facebook Group → Move to Circle at 500+ members

---

### Analytics & Tracking

**Google Analytics 4** (Free)
- Website traffic
- Conversion tracking
- User behavior flows
- E-commerce tracking

**Hotjar** (User Behavior)
- Heatmaps
- Session recordings
- User feedback polls
- Conversion funnel optimization
- Free tier: 35 daily sessions

**Stripe Dashboard**
- Revenue analytics
- Subscription metrics
- Churn tracking
- MRR/ARR reporting

---

### Hosting & Deployment

**Platform:** Vercel
- Next.js optimized hosting
- Automatic deployments from Git
- Preview deployments for testing
- Edge functions
- **Cost:**
  - Free tier for development
  - Pro: $20/month (production)

**Domain:** Hostinger or Namecheap
- pausewell.com (verified available)
- **Cost:** $10-15/year

**SSL:** Automatic via Vercel
- Free SSL certificates
- Automatic renewal

---

### Additional Services

**Video Hosting:** Mux or Vimeo
- Course video content
- Educational videos
- Testimonials
- **Mux:** Pay per minute of video stored/streamed
- **Vimeo Pro:** $20/month

**Image Optimization:** Cloudinary
- Product images
- User-generated content
- Automatic optimization
- Free tier: 25GB/month

**Email Delivery:** Amazon SES (Backup)
- Transactional emails
- Low cost ($0.10 per 1,000 emails)
- High deliverability

---

## 📊 Database Schema

### Core Models (Prisma Schema)

```typescript
// User Management
model User {
  id                String   @id @default(cuid())
  email             String   @unique
  name              String?
  passwordHash      String
  emailVerified     Boolean  @default(false)
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt

  // Relationships
  courseProgress    CourseProgress[]
  membership        Membership?
  orders            Order[]
  communityPosts    Post[]
  profile           UserProfile?
}

// User Profile (Extended Info)
model UserProfile {
  id                String   @id @default(cuid())
  userId            String   @unique
  user              User     @relation(fields: [userId], references: [id])

  age               Int?
  menopauseStage    String?  // perimen opause, menopause, postmenopause
  symptoms          String[] // hot flashes, mood changes, etc.
  healthGoals       String[]
  joinedFrom        String?  // marketing attribution

  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}

// Course System
model Course {
  id                String   @id @default(cuid())
  title             String
  description       String
  price             Decimal
  modules           Module[]
  enrollments       CourseProgress[]

  published         Boolean  @default(false)
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}

model Module {
  id                String   @id @default(cuid())
  courseId          String
  course            Course   @relation(fields: [courseId], references: [id])
  title             String
  order             Int
  lessons           Lesson[]

  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}

model Lesson {
  id                String   @id @default(cuid())
  moduleId          String
  module            Module   @relation(fields: [moduleId], references: [id])
  title             String
  content           String   @db.Text
  videoUrl          String?
  order             Int
  duration          Int?     // minutes

  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}

model CourseProgress {
  id                String   @id @default(cuid())
  userId            String
  user              User     @relation(fields: [userId], references: [id])
  courseId          String
  course            Course   @relation(fields: [courseId], references: [id])

  completed         Boolean  @default(false)
  completedLessons  String[] // Array of lesson IDs
  lastAccessedAt    DateTime @updatedAt
  startedAt         DateTime @default(now())
  completedAt       DateTime?

  @@unique([userId, courseId])
}

// Membership System
model Membership {
  id                String   @id @default(cuid())
  userId            String   @unique
  user              User     @relation(fields: [userId], references: [id])

  status            String   // active, canceled, past_due
  tier              String   // monthly, annual
  stripeCustomerId  String?
  stripeSubId       String?

  currentPeriodEnd  DateTime
  cancelAtPeriodEnd Boolean  @default(false)

  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}

// E-commerce (Supplements)
model Product {
  id                String   @id @default(cuid())
  name              String
  description       String
  price             Decimal
  category          String   // supplement, box, etc.
  inventory         Int      @default(0)
  images            String[] // URLs

  active            Boolean  @default(true)
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt

  orderItems        OrderItem[]
}

model Order {
  id                String   @id @default(cuid())
  userId            String
  user              User     @relation(fields: [userId], references: [id])

  status            String   // pending, paid, shipped, delivered
  total             Decimal
  stripePaymentId   String?

  shippingAddress   String
  trackingNumber    String?

  items             OrderItem[]

  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}

model OrderItem {
  id                String   @id @default(cuid())
  orderId           String
  order             Order    @relation(fields: [orderId], references: [id])
  productId         String
  product           Product  @relation(fields: [productId], references: [id])

  quantity          Int
  price             Decimal

  createdAt         DateTime @default(now())
}

// Community (if custom-built)
model Post {
  id                String   @id @default(cuid())
  userId            String
  user              User     @relation(fields: [userId], references: [id])

  content           String   @db.Text
  likes             Int      @default(0)

  published         Boolean  @default(true)
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}

// Email List (if not using ConvertKit)
model EmailSubscriber {
  id                String   @id @default(cuid())
  email             String   @unique
  name              String?
  status            String   // subscribed, unsubscribed
  source            String?  // lead magnet, checkout, etc.
  tags              String[]

  subscribedAt      DateTime @default(now())
  unsubscribedAt    DateTime?
}
```

---

## 🔐 Authentication & Security

### Authentication Strategy

**NextAuth.js**
- Email/password authentication
- Social login (Google, Facebook) optional
- Session management
- JWT tokens
- Protected routes

**Security Measures:**
- Password hashing (bcrypt)
- CSRF protection
- Rate limiting on API routes
- Input sanitization
- SQL injection prevention (Prisma)
- XSS protection
- HTTPS only

**User Roles:**
- Customer (default)
- Member (active subscription)
- Coach (1:1 coaching clients)
- Admin (team access)

---

## 📧 Email System Architecture

### Transactional Emails (SendGrid)
- Welcome email (on signup)
- Email verification
- Password reset
- Purchase confirmation
- Shipping notification
- Payment failed alerts

### Marketing Emails (ConvertKit)
- Lead magnet delivery
- Course launch sequence
- Membership nurture
- Promotional campaigns
- Educational newsletter
- Re-engagement campaigns

### Automation Flows:
1. **Lead Magnet → Course Funnel**
   - Day 0: Deliver guide
   - Day 1: Share success story
   - Day 3: Introduce course
   - Day 5: Overcome objections
   - Day 7: Last chance offer

2. **Course Purchase → Onboarding**
   - Immediate: Welcome + access
   - Day 1: How to get started
   - Day 3: Module 1 reminder
   - Weekly: Progress check-ins
   - Completion: Membership offer

3. **Membership Cancellation Prevention**
   - 7 days before renewal: Value reminder
   - Failed payment: Recovery email
   - Cancellation: Exit survey + win-back offer

---

## 💳 Payment Flow Architecture

### Stripe Integration

**Setup:**
1. Stripe account configuration
2. Product/price creation in dashboard
3. Webhook endpoints for events
4. Customer portal for self-service

**Payment Types:**

**1. Course (One-Time or Payment Plan)**
- Single payment: $297
- Payment plan: $47 x 7 months
- Stripe Checkout session
- Immediate access on payment

**2. Membership (Recurring)**
- Monthly: $39/month
- Annual: $397/year (save $71)
- Stripe Subscription
- Automatic renewal
- Customer portal access

**3. Supplements (One-Time or Subscription)**
- One-time purchase
- Subscribe & save (15% off)
- Manage in customer portal

**4. Coaching (Invoicing)**
- Stripe invoices
- Payment plans available
- Automatic reminders

**Webhook Events to Handle:**
- `checkout.session.completed` → Grant access
- `invoice.payment_succeeded` → Extend membership
- `invoice.payment_failed` → Send recovery email
- `customer.subscription.deleted` → Remove access
- `customer.subscription.updated` → Update status

---

## 🚀 Deployment Strategy

### Development Workflow

```bash
# Local Development
npm run dev          # Start local server
npm run build        # Test production build
npx prisma studio    # Database GUI
npm run test         # Run tests

# Git Workflow
main branch          → Production (pausewell.com)
staging branch       → Preview (staging.pausewell.com)
feature branches     → PR preview deployments
```

### Deployment Pipeline (Vercel)

1. Push to GitHub
2. Automatic build triggered
3. Preview deployment created
4. Manual review/testing
5. Merge to main
6. Automatic production deployment

### Database Migration Strategy

**Development → Production:**
1. Create migration locally: `npx prisma migrate dev`
2. Test migration in staging
3. Apply to production: `npx prisma migrate deploy`
4. Backup database before major migrations

---

## 📦 Third-Party Integrations

### Course Platform: Teachable (Phase 1)
- API integration for SSO
- Webhook for enrollment tracking
- Embed lessons in site

### Email Marketing: ConvertKit
- API for subscriber management
- Tag automation based on actions
- Form embeds on site

### Community: Facebook Groups (Phase 1)
- Manual invite process
- Link from member dashboard

### Analytics:
- Google Analytics 4 tracking
- Stripe revenue analytics
- ConvertKit email metrics
- Custom dashboard (future)

### Supplement Fulfillment:
- Integration with 3PL (ShipBob, ShipStation)
- Automated order sync
- Tracking updates

---

## 💰 Cost Breakdown

### Monthly Costs

**Development (Month 1-6):**
- Vercel Free Tier: $0
- Teachable Free Trial → Basic: $39
- ConvertKit (0-1K subs): $0
- Facebook Groups: $0
- Domain: $1/month (annual)
- **Total:** ~$40/month

**Production (Month 6-12):**
- Vercel Pro: $20
- Teachable Basic: $39
- ConvertKit (1K-3K): $29
- Stripe fees: 2.9% + $0.30 per transaction
- Mux video: ~$20
- Facebook Groups: $0
- **Total:** ~$108/month + transaction fees

**Scale (Year 2+):**
- Vercel Pro: $20
- Circle Community: $89
- ConvertKit (5K-10K): $79
- Custom course platform: Development time
- Stripe fees: 2.9% + $0.30
- Mux video: ~$50
- **Total:** ~$238/month + transaction fees

---

## 🎯 Technical Roadmap

### Phase 1: MVP (Month 1-3)
- [ ] Set up Next.js project with TypeScript
- [ ] Configure Prisma with SQLite
- [ ] Build landing page
- [ ] Integrate Teachable for course
- [ ] Set up ConvertKit
- [ ] Stripe payment integration (course sales)
- [ ] User authentication (NextAuth)
- [ ] Basic dashboard

### Phase 2: Launch (Month 4-6)
- [ ] Complete course content upload
- [ ] Email automation sequences
- [ ] Facebook Group setup
- [ ] Blog/CMS integration (Sanity)
- [ ] Google Analytics tracking
- [ ] Member dashboard enhancements
- [ ] Deploy to production (Vercel)

### Phase 3: Expand (Month 7-12)
- [ ] Add membership tier
- [ ] E-commerce for supplements
- [ ] Order fulfillment integration
- [ ] Migrate to Circle community
- [ ] Custom course platform (migrate from Teachable)
- [ ] Mobile optimization
- [ ] Advanced analytics dashboard

### Phase 4: Scale (Year 2+)
- [ ] Mobile app (React Native)
- [ ] Corporate portal (B2B)
- [ ] Advanced personalization
- [ ] AI-powered recommendations
- [ ] Expand to international markets
- [ ] White-label offerings

---

## 🛡️ Backup & Disaster Recovery

### Database Backups
- Automatic daily backups (Vercel Postgres)
- Manual backups before major changes
- 30-day retention
- Export scripts for full backup

### Code Backups
- GitHub repository (primary)
- Private repository mirrors
- Tag releases for easy rollback

### Content Backups
- Sanity CMS automatic backups
- Export content monthly
- Video files backed up to S3

### Recovery Plan
- Database restore from backup
- Redeploy from GitHub tag
- DNS failover (if needed)
- Communication plan for outages

---

## 📊 Performance Targets

**Website Speed:**
- First Contentful Paint: <1.5s
- Time to Interactive: <3.5s
- Lighthouse Score: 90+

**Uptime:**
- Target: 99.9% (Vercel SLA)
- Monitoring: Vercel analytics + UptimeRobot

**Scalability:**
- Support 10,000 concurrent users
- Handle 500 course enrollments/day
- Process 100 orders/day

---

## ✅ Technical Architecture Checklist

- [x] Frontend framework selected (Next.js)
- [x] Backend/database planned (Prisma + PostgreSQL)
- [x] CMS chosen (Sanity.io)
- [x] Course platform decided (Teachable → Custom)
- [x] Payment processor integrated (Stripe)
- [x] Email marketing platform (ConvertKit)
- [x] Community platform (Facebook → Circle)
- [x] Hosting provider (Vercel)
- [x] Domain strategy (pausewell.com)
- [x] Database schema designed
- [x] Security measures planned
- [x] Deployment pipeline defined
- [x] Cost breakdown calculated
- [x] Technical roadmap created

---

**Document Created:** November 2, 2025
**Status:** Technical Architecture Complete ✅
**Next Document:** Affiliate Monetization Plan.md

---

*Part of PAUSEWELL brand system*
*Portfolio Brand #19 for Side-Brands*
