# Technical Architecture - AI Content Studio

## System Overview
Comprehensive AI tools education platform featuring tool reviews, tutorials, affiliate marketplace, community forums, and training programs built on modern JAMstack architecture with headless CMS.

## Technology Stack

### Frontend
**Framework**: Next.js 14 with TypeScript
```typescript
// Platform Configuration
const PlatformConfig = {
  framework: 'Next.js 14',
  language: 'TypeScript',
  ui: {
    library: 'React 18',
    styling: 'Tailwind CSS + CSS Modules',
    components: 'Radix UI + shadcn/ui',
    animations: 'Framer Motion',
    charts: 'Recharts'
  },
  features: {
    search: 'Algolia',
    analytics: 'Plausible + Mixpanel',
    comments: 'Disqus',
    newsletter: 'ConvertKit API',
    payments: 'Stripe + PayPal'
  }
};
```

**Core Components**:
- Tool comparison engine
- Interactive tutorials
- Affiliate link manager
- Resource library
- Community forums
- Course platform
- Email capture forms
- Search and filters

### Backend Architecture

**Microservices Design**:
```javascript
const services = {
  contentService: {
    purpose: 'Content management and delivery',
    tech: 'Strapi CMS',
    database: 'PostgreSQL',
    cdn: 'Cloudflare',
    features: ['Reviews', 'Tutorials', 'Blog posts']
  },
  affiliateService: {
    purpose: 'Affiliate link management',
    tech: 'Node.js + Express',
    database: 'PostgreSQL',
    cache: 'Redis',
    features: ['Link tracking', 'Commission tracking', 'Analytics']
  },
  userService: {
    purpose: 'User management and auth',
    tech: 'Node.js + Express',
    auth: 'Auth0',
    database: 'PostgreSQL',
    features: ['Profiles', 'Preferences', 'Progress tracking']
  },
  courseService: {
    purpose: 'Training and education',
    tech: 'Node.js + Express',
    lms: 'Custom + Teachable API',
    database: 'MongoDB',
    features: ['Video hosting', 'Progress', 'Certificates']
  },
  analyticsService: {
    purpose: 'Data tracking and insights',
    tech: 'Python + FastAPI',
    database: 'ClickHouse',
    features: ['User behavior', 'Conversion tracking', 'Reports']
  }
};
```

### Database Architecture

**PostgreSQL - Primary Data**:
```sql
-- User accounts and profiles
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(50) UNIQUE,
    full_name VARCHAR(255),
    avatar_url VARCHAR(500),
    bio TEXT,
    expertise_level VARCHAR(20), -- beginner, intermediate, expert
    interests JSONB,
    subscription_tier VARCHAR(20) DEFAULT 'free',
    created_at TIMESTAMP DEFAULT NOW(),
    last_active TIMESTAMP
);

-- AI tools database
CREATE TABLE tools (
    id UUID PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE,
    category VARCHAR(100),
    subcategory VARCHAR(100),
    description TEXT,
    features JSONB,
    pricing JSONB,
    pros TEXT[],
    cons TEXT[],
    rating DECIMAL(3,2),
    review_count INTEGER DEFAULT 0,
    affiliate_link VARCHAR(500),
    commission_rate DECIMAL(5,2),
    official_url VARCHAR(500),
    last_updated TIMESTAMP
);

-- Tool reviews and comparisons
CREATE TABLE reviews (
    id UUID PRIMARY KEY,
    tool_id UUID REFERENCES tools(id),
    author_id UUID REFERENCES users(id),
    title VARCHAR(255),
    content TEXT,
    rating INTEGER CHECK (rating >= 1 AND rating <= 5),
    test_duration_days INTEGER,
    publish_date TIMESTAMP,
    update_date TIMESTAMP,
    view_count INTEGER DEFAULT 0,
    helpful_count INTEGER DEFAULT 0,
    meta_description TEXT,
    featured BOOLEAN DEFAULT false
);

-- Tutorials and guides
CREATE TABLE tutorials (
    id UUID PRIMARY KEY,
    title VARCHAR(255),
    slug VARCHAR(255) UNIQUE,
    description TEXT,
    content TEXT,
    tools_used UUID[],
    difficulty_level VARCHAR(20),
    duration_minutes INTEGER,
    video_url VARCHAR(500),
    github_repo VARCHAR(500),
    downloads INTEGER DEFAULT 0,
    completion_rate DECIMAL(5,2)
);

-- Affiliate tracking
CREATE TABLE affiliate_clicks (
    id UUID PRIMARY KEY,
    tool_id UUID REFERENCES tools(id),
    user_id UUID REFERENCES users(id),
    click_date TIMESTAMP DEFAULT NOW(),
    converted BOOLEAN DEFAULT false,
    commission_amount DECIMAL(10,2),
    ip_address INET,
    referrer VARCHAR(500),
    device_type VARCHAR(50),
    country VARCHAR(2)
);

-- Course enrollments
CREATE TABLE enrollments (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    course_id VARCHAR(100),
    enrollment_date TIMESTAMP DEFAULT NOW(),
    progress_percentage INTEGER DEFAULT 0,
    completed BOOLEAN DEFAULT false,
    certificate_issued BOOLEAN DEFAULT false,
    last_accessed TIMESTAMP
);
```

**MongoDB - Content & Analytics**:
```javascript
// User activity tracking
const ActivitySchema = {
  userId: ObjectId,
  sessionId: String,
  events: [{
    type: String, // pageview, click, scroll, video_play
    timestamp: Date,
    page: String,
    element: String,
    value: Mixed
  }],
  duration: Number,
  bounced: Boolean
};

// Content performance metrics
const ContentMetricsSchema = {
  contentId: String,
  type: String, // review, tutorial, blog
  period: Date,
  metrics: {
    views: Number,
    uniqueVisitors: Number,
    avgTimeOnPage: Number,
    bounceRate: Number,
    shares: Number,
    conversions: Number,
    revenue: Number
  }
};

// Email campaign data
const EmailCampaignSchema = {
  campaignId: String,
  subject: String,
  sentDate: Date,
  recipients: Number,
  opens: Number,
  clicks: Number,
  conversions: Number,
  revenue: Number,
  unsubscribes: Number
};
```

## Content Management System

### Headless CMS (Strapi)
```javascript
// Content Types Configuration
const contentTypes = {
  Review: {
    fields: {
      title: 'String',
      slug: 'UID',
      tool: 'Relation',
      content: 'RichText',
      rating: 'Number',
      pros: 'JSON',
      cons: 'JSON',
      verdict: 'Text',
      alternatives: 'Relation'
    }
  },
  Tutorial: {
    fields: {
      title: 'String',
      slug: 'UID',
      description: 'Text',
      content: 'RichText',
      video: 'Media',
      codeExamples: 'JSON',
      tools: 'Relation',
      difficulty: 'Enumeration'
    }
  },
  Comparison: {
    fields: {
      title: 'String',
      tools: 'Relation',
      criteria: 'Component',
      winner: 'Relation',
      matrix: 'JSON'
    }
  }
};
```

## Search & Discovery

### Algolia Integration
```typescript
class SearchEngine {
  private algoliaClient: SearchClient;
  
  async indexContent(content: Content): Promise<void> {
    const record = {
      objectID: content.id,
      title: content.title,
      description: content.description,
      type: content.type,
      category: content.category,
      tags: content.tags,
      rating: content.rating,
      url: content.url,
      thumbnail: content.thumbnail,
      _geoloc: content.location
    };
    
    await this.algoliaClient.saveObject(record);
  }
  
  async search(query: string, filters?: SearchFilters): Promise<SearchResults> {
    const searchParams = {
      query,
      filters: this.buildFilters(filters),
      facets: ['category', 'type', 'rating'],
      hitsPerPage: 20
    };
    
    return await this.algoliaClient.search(searchParams);
  }
}
```

## Affiliate Management

### Link Management System
```typescript
class AffiliateManager {
  async createTrackedLink(toolId: string, source: string): Promise<string> {
    const tool = await this.getTool(toolId);
    const trackingId = this.generateTrackingId();
    
    // Store tracking data
    await this.redis.setex(
      `tracking:${trackingId}`,
      86400, // 24 hours
      JSON.stringify({ toolId, source, timestamp: Date.now() })
    );
    
    // Return cloaked URL
    return `${process.env.DOMAIN}/go/${trackingId}`;
  }
  
  async handleClick(trackingId: string, request: Request): Promise<void> {
    const trackingData = await this.redis.get(`tracking:${trackingId}`);
    
    if (trackingData) {
      // Log click
      await this.logClick({
        ...JSON.parse(trackingData),
        ip: request.ip,
        userAgent: request.headers['user-agent'],
        referer: request.headers.referer
      });
      
      // Redirect to affiliate link
      const tool = await this.getTool(trackingData.toolId);
      return tool.affiliateLink;
    }
  }
}
```

## Performance Optimization

### Caching Strategy
```typescript
const CachingConfig = {
  cdn: {
    provider: 'Cloudflare',
    ttl: {
      static: '1 year',
      images: '1 month',
      api: '5 minutes',
      html: '1 hour'
    }
  },
  redis: {
    sessions: '24 hours',
    apiCache: '5 minutes',
    toolData: '1 hour',
    userPreferences: '7 days'
  },
  browser: {
    localStorage: ['preferences', 'recentTools'],
    sessionStorage: ['filters', 'searchHistory']
  }
};
```

### Image Optimization
```javascript
const imageOptimization = {
  formats: ['webp', 'avif', 'jpg'],
  sizes: [320, 640, 1024, 1920],
  lazy: true,
  placeholder: 'blur',
  quality: 85
};
```

## API Architecture

### RESTful API Endpoints
```typescript
// Public API
GET /api/tools
GET /api/tools/:slug
GET /api/reviews/:slug
GET /api/tutorials
GET /api/comparisons
POST /api/newsletter/subscribe

// Authenticated API
GET /api/user/profile
PUT /api/user/preferences
GET /api/user/history
POST /api/tools/:id/bookmark
GET /api/courses/progress

// Admin API
POST /api/admin/tools
PUT /api/admin/reviews/:id
DELETE /api/admin/content/:id
GET /api/admin/analytics
```

## Email Marketing Integration

### ConvertKit Setup
```typescript
class EmailMarketing {
  async subscribeUser(email: string, tags: string[]): Promise<void> {
    await this.convertkit.subscribe({
      email,
      tags,
      fields: {
        source: 'website',
        interests: tags.join(',')
      }
    });
    
    // Trigger welcome sequence
    await this.convertkit.addToSequence(email, 'welcome_series');
  }
  
  async sendBroadcast(segment: string, content: EmailContent): Promise<void> {
    const subscribers = await this.convertkit.getSegment(segment);
    
    await this.convertkit.createBroadcast({
      subject: content.subject,
      content: content.body,
      recipients: subscribers,
      tracking: true
    });
  }
}
```

## Analytics & Tracking

### Event Tracking
```typescript
const Analytics = {
  events: {
    pageView: (page: string) => {
      plausible('pageview', { props: { page } });
      mixpanel.track('Page View', { page });
    },
    toolClick: (toolId: string, position: number) => {
      plausible('Tool Click', { props: { toolId, position } });
      mixpanel.track('Tool Click', { toolId, position });
    },
    conversion: (toolId: string, value: number) => {
      plausible('Conversion', { props: { toolId, value } });
      mixpanel.track('Conversion', { toolId, value });
    }
  }
};
```

## Infrastructure

### Deployment Configuration
```yaml
# Vercel deployment
{
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/next"
    }
  ],
  "env": {
    "DATABASE_URL": "@database_url",
    "REDIS_URL": "@redis_url",
    "ALGOLIA_API_KEY": "@algolia_api_key",
    "CONVERTKIT_API_KEY": "@convertkit_api_key"
  },
  "regions": ["iad1", "sfo1", "lhr1"]
}
```

### Docker Setup
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

## Security

### Security Measures
```typescript
const Security = {
  headers: {
    'X-Frame-Options': 'DENY',
    'X-Content-Type-Options': 'nosniff',
    'X-XSS-Protection': '1; mode=block',
    'Content-Security-Policy': "default-src 'self'"
  },
  rateLimit: {
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100 // limit each IP to 100 requests
  },
  validation: {
    email: Joi.string().email(),
    url: Joi.string().uri(),
    content: DOMPurify.sanitize
  }
};
```

## Cost Analysis

### Monthly Infrastructure Costs
- Hosting (Vercel): $20-100
- Database (Supabase): $25-50
- CMS (Strapi Cloud): $29-99
- CDN (Cloudflare): $20-50
- Email (ConvertKit): $29-79
- Analytics: $20-40
- Search (Algolia): $50-150
- **Total**: $193-568/month

---

*AI Content Studio's architecture is designed for scalability, performance, and optimal user experience in AI education.*