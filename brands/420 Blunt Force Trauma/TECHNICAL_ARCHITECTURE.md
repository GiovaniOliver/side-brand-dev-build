# Technical Architecture - 420 Blunt Force Trauma

## System Overview
Cannabis culture platform featuring strain reviews, dispensary locations, community forums, educational content, and e-commerce integration built with compliance-first architecture for multi-state regulations.

## Technology Stack

### Frontend
**Framework**: Next.js 14 with TypeScript
```typescript
// Cannabis platform configuration
const PlatformConfig = {
  framework: 'Next.js 14',
  language: 'TypeScript',
  styling: 'Tailwind CSS + styled-components',
  ui: {
    library: 'Chakra UI',
    theme: 'Cannabis Green palette',
    animations: 'Framer Motion',
    accessibility: 'WCAG 2.1 AA compliant'
  },
  features: {
    ageGate: 'Required (21+)',
    geoBlocking: 'State-based restrictions',
    search: 'Algolia with filters',
    maps: 'Mapbox for dispensaries'
  }
};
```

**Core Components**:
- Age verification gate
- Strain database browser
- Dispensary map interface
- Product review system
- Educational content hub
- Community forums
- Shopping cart (where legal)

### Backend Architecture

**Microservices Structure**:
```javascript
const services = {
  complianceService: {
    purpose: 'Age verification and geo-restrictions',
    tech: 'Node.js + Express',
    features: ['IP geolocation', 'Age gate', 'State laws API'],
    database: 'PostgreSQL'
  },
  strainService: {
    purpose: 'Cannabis strain information',
    tech: 'Python + FastAPI',
    database: 'PostgreSQL + ElasticSearch',
    cache: 'Redis'
  },
  dispensaryService: {
    purpose: 'Dispensary locations and inventory',
    tech: 'Node.js + GraphQL',
    database: 'MongoDB',
    integration: 'Third-party APIs'
  },
  communityService: {
    purpose: 'Forums and user content',
    tech: 'Node.js + Express',
    database: 'PostgreSQL',
    moderation: 'AI + Manual'
  },
  contentService: {
    purpose: 'Educational content management',
    tech: 'Strapi CMS',
    storage: 'AWS S3',
    cdn: 'CloudFront'
  }
};
```

### Database Architecture

**PostgreSQL - Core Data**:
```sql
-- User accounts with compliance tracking
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    username VARCHAR(50) UNIQUE,
    email VARCHAR(255) UNIQUE,
    age_verified BOOLEAN DEFAULT false,
    age_verified_date TIMESTAMP,
    state VARCHAR(2),
    medical_card BOOLEAN DEFAULT false,
    preferences JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Cannabis strains database
CREATE TABLE strains (
    id UUID PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(20), -- Sativa, Indica, Hybrid
    thc_content DECIMAL(4,2),
    cbd_content DECIMAL(4,2),
    terpenes JSONB,
    effects TEXT[],
    medical_uses TEXT[],
    flavors TEXT[],
    description TEXT,
    lineage JSONB,
    average_rating DECIMAL(3,2),
    review_count INTEGER DEFAULT 0
);

-- Dispensary listings
CREATE TABLE dispensaries (
    id UUID PRIMARY KEY,
    name VARCHAR(255),
    license_number VARCHAR(100),
    state VARCHAR(2),
    city VARCHAR(100),
    address TEXT,
    coordinates POINT,
    phone VARCHAR(20),
    website VARCHAR(255),
    hours JSONB,
    services TEXT[],
    verified BOOLEAN DEFAULT false,
    rating DECIMAL(3,2),
    active BOOLEAN DEFAULT true
);

-- Product reviews with compliance
CREATE TABLE reviews (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    strain_id UUID REFERENCES strains(id),
    dispensary_id UUID REFERENCES dispensaries(id),
    rating INTEGER CHECK (rating >= 1 AND rating <= 5),
    title VARCHAR(255),
    content TEXT,
    effects_experienced TEXT[],
    medical_benefits TEXT[],
    verified_purchase BOOLEAN DEFAULT false,
    helpful_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Educational content
CREATE TABLE content (
    id UUID PRIMARY KEY,
    type VARCHAR(50), -- article, guide, video, infographic
    title VARCHAR(255),
    slug VARCHAR(255) UNIQUE,
    content JSONB,
    author_id UUID,
    category VARCHAR(50),
    tags TEXT[],
    medical_reviewed BOOLEAN DEFAULT false,
    state_specific VARCHAR(2),
    published BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT NOW()
);
```

**MongoDB - Flexible Data**:
```javascript
// Dispensary inventory schema
const InventorySchema = {
  dispensaryId: ObjectId,
  lastUpdated: Date,
  products: [{
    strainId: String,
    name: String,
    type: String, // flower, edible, concentrate, etc.
    thc: Number,
    cbd: Number,
    price: {
      gram: Number,
      eighth: Number,
      quarter: Number,
      half: Number,
      ounce: Number
    },
    inStock: Boolean,
    quantity: Number
  }],
  deals: [{
    name: String,
    description: String,
    validUntil: Date,
    discount: Number
  }]
};

// User activity tracking
const ActivitySchema = {
  userId: ObjectId,
  activities: [{
    type: String, // view, review, save, share
    targetType: String, // strain, dispensary, content
    targetId: String,
    timestamp: Date,
    metadata: Object
  }],
  preferences: {
    favoriteStrains: [String],
    effects: [String],
    avoidEffects: [String],
    medicalNeeds: [String]
  }
};
```

## Compliance & Security

### Age Verification System
```typescript
class AgeVerification {
  private verificationMethods = {
    selfAttestation: {
      required: true,
      level: 'basic'
    },
    documentUpload: {
      required: false,
      level: 'enhanced',
      provider: 'Jumio'
    },
    creditCardVerification: {
      required: false,
      level: 'enhanced'
    }
  };
  
  async verifyAge(user: User): Promise<VerificationResult> {
    // IP-based location check
    const location = await this.getLocation(user.ip);
    const stateRequirements = this.getStateRequirements(location.state);
    
    // Age gate implementation
    if (user.birthDate) {
      const age = this.calculateAge(user.birthDate);
      if (age < stateRequirements.minAge) {
        return { verified: false, reason: 'Under age' };
      }
    }
    
    // Enhanced verification for certain states
    if (stateRequirements.enhancedVerification) {
      return await this.enhancedVerification(user);
    }
    
    return { verified: true };
  }
  
  async getStateCompliance(state: string): Promise<StateCompliance> {
    return {
      medicalOnly: this.isMedicalOnly(state),
      recreationalAge: this.getRecreationalAge(state),
      deliveryAllowed: this.isDeliveryAllowed(state),
      advertisingRestrictions: this.getAdRestrictions(state)
    };
  }
}
```

### Geo-Blocking & Restrictions
```typescript
class GeoCompliance {
  private restrictedStates = ['ID', 'KS', 'SC', 'WY']; // Example
  private medicalOnlyStates = ['FL', 'PA', 'OH']; // Example
  
  async checkAccess(request: Request): Promise<AccessResult> {
    const location = await this.getLocationFromIP(request.ip);
    
    if (this.restrictedStates.includes(location.state)) {
      return {
        allowed: false,
        redirect: '/restricted-access',
        message: 'Cannabis content not available in your location'
      };
    }
    
    if (this.medicalOnlyStates.includes(location.state)) {
      return {
        allowed: true,
        restrictions: ['medical-only', 'no-recreational'],
        features: this.getMedicalOnlyFeatures()
      };
    }
    
    return {
      allowed: true,
      restrictions: [],
      features: this.getFullFeatures()
    };
  }
}
```

## API Architecture

### RESTful Endpoints
```typescript
// Public API
GET /api/strains
GET /api/strains/:id
GET /api/strains/search
GET /api/dispensaries
GET /api/dispensaries/nearby
GET /api/content/educational

// Authenticated endpoints
POST /api/reviews
GET /api/users/preferences
POST /api/users/favorites
GET /api/users/recommendations

// Compliance endpoints
POST /api/verify/age
GET /api/compliance/state/:state
POST /api/report/violation
```

### Third-Party Integrations
```javascript
const integrations = {
  compliance: {
    ageVerification: 'Jumio',
    stateRegulations: 'Cannabiz Media',
    licensing: 'Metrc API'
  },
  data: {
    strainDatabase: 'Leafly API',
    dispensaryData: 'Weedmaps API',
    labResults: 'Confident Cannabis'
  },
  payment: {
    processor: 'Paybotic', // Cannabis-friendly
    crypto: 'BitPay',
    cashApp: 'Square'
  },
  marketing: {
    email: 'Mailchimp',
    sms: 'Twilio',
    push: 'OneSignal'
  }
};
```

## Features Implementation

### 1. Strain Discovery Engine
```typescript
class StrainRecommendation {
  async getRecommendations(user: User): Promise<Strain[]> {
    // Analyze user preferences
    const preferences = await this.getUserPreferences(user.id);
    
    // Machine learning model for recommendations
    const model = await this.loadRecommendationModel();
    
    // Factor in medical needs
    const medicalFactors = preferences.medicalNeeds || [];
    
    // Get similar strains
    const recommendations = await model.predict({
      favoriteStrains: preferences.favorites,
      desiredEffects: preferences.effects,
      avoidEffects: preferences.avoid,
      medicalNeeds: medicalFactors,
      previousRatings: await this.getUserRatings(user.id)
    });
    
    // Filter by availability
    return this.filterByLocalAvailability(recommendations, user.location);
  }
}
```

### 2. Dispensary Finder
```typescript
class DispensaryFinder {
  async findNearby(location: Location, filters: Filters): Promise<Dispensary[]> {
    const query = {
      location: {
        $near: {
          $geometry: {
            type: 'Point',
            coordinates: [location.lng, location.lat]
          },
          $maxDistance: filters.radius || 10000 // meters
        }
      },
      active: true,
      ...(filters.medical && { services: 'medical' }),
      ...(filters.recreational && { services: 'recreational' }),
      ...(filters.delivery && { services: 'delivery' })
    };
    
    const dispensaries = await this.db.dispensaries.find(query);
    
    // Enrich with real-time data
    return Promise.all(dispensaries.map(async d => ({
      ...d,
      inventory: await this.getInventory(d.id),
      waitTime: await this.getWaitTime(d.id),
      deals: await this.getCurrentDeals(d.id)
    })));
  }
}
```

### 3. Community Moderation
```typescript
class ContentModeration {
  private bannedTerms = [...]; // Compliance-required terms
  private medicalClaims = [...]; // FDA-restricted claims
  
  async moderateContent(content: Content): Promise<ModerationResult> {
    // AI-powered moderation
    const aiCheck = await this.runAIModeration(content);
    
    // Compliance checks
    const complianceCheck = this.checkCompliance(content);
    
    // Medical claims check
    const medicalCheck = this.checkMedicalClaims(content);
    
    if (!complianceCheck.passed || !medicalCheck.passed) {
      return {
        approved: false,
        reasons: [...complianceCheck.issues, ...medicalCheck.issues],
        requiresReview: true
      };
    }
    
    return {
      approved: aiCheck.confidence > 0.8,
      requiresReview: aiCheck.confidence <= 0.8
    };
  }
}
```

## Infrastructure

### Deployment Configuration
```yaml
# Docker compose for multi-service deployment
version: '3.8'
services:
  web:
    build: ./web
    environment:
      - NODE_ENV=production
      - NEXT_PUBLIC_API_URL=${API_URL}
    ports:
      - "3000:3000"
  
  api:
    build: ./api
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - REDIS_URL=${REDIS_URL}
    ports:
      - "4000:4000"
  
  compliance:
    build: ./compliance
    environment:
      - JUMIO_API_KEY=${JUMIO_API_KEY}
      - GEO_DB_PATH=/data/geo.db
  
  postgres:
    image: postgres:15-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:7-alpine
  
  elasticsearch:
    image: elasticsearch:8.8.0
    environment:
      - discovery.type=single-node
```

### CDN & Caching Strategy
```typescript
const CachingStrategy = {
  cdn: {
    provider: 'CloudFlare',
    zones: ['US', 'Canada'],
    rules: {
      images: '1 year',
      static: '1 month',
      api: 'no-cache'
    }
  },
  redis: {
    strainData: '1 hour',
    dispensaryInfo: '15 minutes',
    userSessions: '24 hours',
    searchResults: '5 minutes'
  },
  browser: {
    localStorage: ['preferences', 'ageVerification'],
    sessionStorage: ['cart', 'recentViews']
  }
};
```

## Monitoring & Analytics

### Performance Tracking
```typescript
const Monitoring = {
  apm: 'DataDog',
  errors: 'Sentry',
  analytics: 'Google Analytics 4',
  custom: {
    strainViews: 'Track popular strains',
    searchTerms: 'Optimize search',
    userJourney: 'Conversion tracking',
    complianceEvents: 'Age gate, geo-blocks'
  }
};
```

## Cost Structure

### Monthly Infrastructure Costs
- Hosting (AWS/Vercel): $300-600
- Databases: $200-400
- CDN: $100-200
- Third-party APIs: $500-1,000
- Compliance services: $200-500
- Monitoring: $100-200
- **Total**: $1,400-2,900/month

---

*420 Blunt Force Trauma's architecture prioritizes compliance, user safety, and scalability while providing a comprehensive cannabis information platform.*