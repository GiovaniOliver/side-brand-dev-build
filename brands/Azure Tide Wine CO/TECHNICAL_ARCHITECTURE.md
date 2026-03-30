# Technical Architecture - Azure Tide Wine CO

## System Overview
Luxury wine e-commerce platform with ocean conservation integration, featuring wine recommendations, subscription boxes, virtual tastings, marine life adoption program, and blockchain-verified sustainability tracking.

## Technology Stack

### Frontend
**Framework**: Next.js 14 with TypeScript
```typescript
// Azure Tide Wine CO Platform Configuration
const PlatformConfig = {
  framework: 'Next.js 14',
  language: 'TypeScript',
  ui: {
    library: 'React 18',
    styling: 'Tailwind CSS + Emotion',
    components: 'MUI + Custom luxury components',
    animations: 'Framer Motion + GSAP',
    3d: 'Three.js for wine bottles'
  },
  features: {
    ar: 'AR.js for wine label scanning',
    maps: 'Mapbox for vineyard locations',
    video: 'Agora for virtual tastings',
    payments: 'Stripe + Crypto',
    tracking: 'Blockchain for sustainability'
  }
};
```

**Core Components**:
- Wine catalog with advanced filtering
- AI sommelier recommendations
- Subscription management
- Virtual tasting room
- Marine adoption tracker
- Impact dashboard
- AR wine label scanner
- Wine cellar manager

### Backend Architecture

**Microservices Design**:
```javascript
const services = {
  catalogService: {
    purpose: 'Wine inventory and details',
    tech: 'Node.js + Express',
    database: 'PostgreSQL',
    cache: 'Redis',
    features: ['Search', 'Filtering', 'Recommendations']
  },
  subscriptionService: {
    purpose: 'Subscription box management',
    tech: 'Node.js + Express',
    payments: 'Stripe',
    database: 'PostgreSQL',
    features: ['Billing', 'Customization', 'Scheduling']
  },
  conservationService: {
    purpose: 'Ocean conservation tracking',
    tech: 'Python + FastAPI',
    blockchain: 'Polygon',
    database: 'MongoDB',
    features: ['Impact tracking', 'Adoption program', 'Donations']
  },
  recommendationService: {
    purpose: 'AI wine recommendations',
    tech: 'Python + TensorFlow',
    ml: 'Collaborative filtering + Content-based',
    database: 'PostgreSQL + Neo4j'
  },
  tastingService: {
    purpose: 'Virtual tasting events',
    tech: 'Node.js + WebRTC',
    video: 'Agora SDK',
    database: 'MongoDB',
    features: ['Live streaming', 'Chat', 'Recording']
  }
};
```

### Database Architecture

**PostgreSQL - Core E-commerce**:
```sql
-- Customer accounts with preferences
CREATE TABLE customers (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255),
    wine_preferences JSONB,
    taste_profile JSONB,
    subscription_tier VARCHAR(50),
    conservation_supporter BOOLEAN DEFAULT false,
    lifetime_value DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Wine catalog
CREATE TABLE wines (
    id UUID PRIMARY KEY,
    name VARCHAR(255),
    winery VARCHAR(255),
    region VARCHAR(100),
    country VARCHAR(100),
    vintage INTEGER,
    type VARCHAR(50), -- red, white, rosé, sparkling
    varietal VARCHAR(100),
    alcohol_content DECIMAL(3,1),
    tasting_notes TEXT,
    description TEXT,
    price DECIMAL(10,2),
    member_price DECIMAL(10,2),
    inventory_count INTEGER,
    sustainability_score INTEGER,
    ocean_impact_percentage DECIMAL(5,2),
    ratings JSONB,
    food_pairings TEXT[],
    image_urls TEXT[]
);

-- Subscription boxes
CREATE TABLE subscription_boxes (
    id UUID PRIMARY KEY,
    customer_id UUID REFERENCES customers(id),
    type VARCHAR(50), -- explorer, connoisseur, collector
    frequency VARCHAR(20), -- monthly, quarterly
    bottle_count INTEGER,
    price_range VARCHAR(50),
    preferences JSONB,
    next_shipment DATE,
    status VARCHAR(20) DEFAULT 'active'
);

-- Marine adoptions
CREATE TABLE marine_adoptions (
    id UUID PRIMARY KEY,
    customer_id UUID REFERENCES customers(id),
    species VARCHAR(100),
    individual_name VARCHAR(100),
    adoption_date DATE,
    monthly_contribution DECIMAL(10,2),
    impact_metrics JSONB,
    certificate_url VARCHAR(255),
    tracking_data JSONB
);

-- Virtual tastings
CREATE TABLE virtual_tastings (
    id UUID PRIMARY KEY,
    title VARCHAR(255),
    host_name VARCHAR(255),
    scheduled_time TIMESTAMP,
    duration_minutes INTEGER,
    max_participants INTEGER,
    current_participants INTEGER DEFAULT 0,
    wines UUID[],
    price DECIMAL(10,2),
    recording_url VARCHAR(255),
    status VARCHAR(20) DEFAULT 'upcoming'
);

-- Orders with conservation tracking
CREATE TABLE orders (
    id UUID PRIMARY KEY,
    customer_id UUID REFERENCES customers(id),
    order_date TIMESTAMP DEFAULT NOW(),
    items JSONB,
    subtotal DECIMAL(10,2),
    conservation_contribution DECIMAL(10,2),
    shipping DECIMAL(10,2),
    total DECIMAL(10,2),
    status VARCHAR(50),
    tracking_number VARCHAR(100),
    carbon_offset_kg DECIMAL(10,2),
    ocean_impact_data JSONB
);
```

**MongoDB - Content & Analytics**:
```javascript
// Wine reviews and ratings
const ReviewSchema = {
  wineId: ObjectId,
  customerId: ObjectId,
  rating: Number,
  tastingNotes: {
    appearance: String,
    aroma: String,
    taste: String,
    finish: String
  },
  foodPairings: [String],
  occasion: String,
  wouldRecommend: Boolean,
  verifiedPurchase: Boolean,
  helpful: Number,
  images: [String],
  createdAt: Date
};

// Conservation impact tracking
const ImpactSchema = {
  customerId: ObjectId,
  period: {
    start: Date,
    end: Date
  },
  metrics: {
    bottlesPurchased: Number,
    conservationDonated: Number,
    plasticOffsetKg: Number,
    marineSpeciesSupported: Number,
    reefAreaProtectedM2: Number,
    cleanupEventsSponsored: Number
  },
  achievements: [{
    type: String,
    earnedAt: Date,
    description: String
  }],
  blockchainTxHash: String
};
```

## Wine Recommendation Engine

### AI Sommelier System
```python
import numpy as np
import pandas as pd
from sklearn.ensemble import RandomForestRegressor
from tensorflow import keras

class WineSommelier:
    def __init__(self):
        self.taste_model = self.load_taste_model()
        self.pairing_model = self.load_pairing_model()
        self.user_profiles = {}
        
    def recommend_wines(self, user_id, context=None):
        """Generate personalized wine recommendations"""
        
        # Get user taste profile
        profile = self.get_user_profile(user_id)
        
        # Consider context (meal, occasion, season)
        if context:
            context_weights = self.get_context_weights(context)
        else:
            context_weights = np.ones(10)
        
        # Generate recommendations
        recommendations = []
        
        # Content-based filtering
        content_recs = self.content_based_recommendations(
            profile.taste_preferences,
            profile.previous_ratings
        )
        
        # Collaborative filtering
        collab_recs = self.collaborative_recommendations(
            user_id,
            profile.similar_users
        )
        
        # Hybrid approach with conservation factor
        for wine in self.merge_recommendations(content_recs, collab_recs):
            score = self.calculate_score(wine, profile, context_weights)
            
            # Boost score for high sustainability wines
            if wine.sustainability_score > 8:
                score *= 1.2
            
            recommendations.append({
                'wine': wine,
                'score': score,
                'reason': self.generate_reason(wine, profile)
            })
        
        return sorted(recommendations, key=lambda x: x['score'], reverse=True)[:10]
    
    def food_pairing_suggestions(self, meal_description):
        """Suggest wines based on food"""
        
        # Extract meal features
        features = self.extract_meal_features(meal_description)
        
        # Use trained pairing model
        wine_profiles = self.pairing_model.predict(features)
        
        # Find matching wines
        matches = self.find_matching_wines(wine_profiles)
        
        return matches
```

### Subscription Curation Algorithm
```python
class SubscriptionCurator:
    def create_monthly_selection(self, subscriber):
        """Curate personalized monthly wine selection"""
        
        selection = []
        budget = subscriber.price_range
        preferences = subscriber.preferences
        
        # Ensure variety
        categories = {
            'discovery': 0.3,  # New wines to try
            'favorite': 0.4,   # Based on past ratings
            'seasonal': 0.2,   # Seasonal selections
            'conservation': 0.1  # High-impact wines
        }
        
        for category, allocation in categories.items():
            category_budget = budget * allocation
            
            if category == 'discovery':
                wines = self.get_discovery_wines(
                    preferences,
                    subscriber.tried_wines
                )
            elif category == 'favorite':
                wines = self.get_favorite_style_wines(
                    subscriber.top_rated_wines
                )
            elif category == 'seasonal':
                wines = self.get_seasonal_wines(
                    self.current_season()
                )
            elif category == 'conservation':
                wines = self.get_high_impact_wines()
            
            selection.extend(
                self.select_within_budget(wines, category_budget)
            )
        
        return self.optimize_selection(selection, budget)
```

## Conservation Integration

### Blockchain Impact Tracking
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract OceanConservation {
    struct ImpactRecord {
        address customer;
        uint256 contribution;
        uint256 plasticOffset;
        uint256 marineAreaProtected;
        string adoptedSpecies;
        uint256 timestamp;
    }
    
    mapping(address => ImpactRecord[]) public customerImpact;
    mapping(address => uint256) public totalContributions;
    
    event ConservationContribution(
        address indexed customer,
        uint256 amount,
        uint256 impact
    );
    
    function recordPurchaseImpact(
        address _customer,
        uint256 _orderAmount
    ) external {
        uint256 contribution = (_orderAmount * 10) / 100; // 10% to conservation
        uint256 plasticOffset = contribution * 5; // 5kg per dollar
        uint256 marineArea = contribution * 2; // 2 sq meters per dollar
        
        ImpactRecord memory record = ImpactRecord({
            customer: _customer,
            contribution: contribution,
            plasticOffset: plasticOffset,
            marineAreaProtected: marineArea,
            adoptedSpecies: "",
            timestamp: block.timestamp
        });
        
        customerImpact[_customer].push(record);
        totalContributions[_customer] += contribution;
        
        emit ConservationContribution(_customer, contribution, plasticOffset);
    }
    
    function adoptMarineLife(
        address _customer,
        string memory _species,
        uint256 _monthlyAmount
    ) external {
        // Record adoption on blockchain
        // Implementation details...
    }
}
```

### Marine Life Tracking API
```typescript
class MarineTracker {
  async trackMarinePartner(adoptionId: string): Promise<TrackingData> {
    // Connect to marine research APIs
    const partners = [
      'OceanConservancy',
      'MarineTrafficAPI',
      'WhaleAlert'
    ];
    
    const trackingData = await Promise.all(
      partners.map(p => this.fetchTrackingData(p, adoptionId))
    );
    
    return {
      lastSighting: this.getLatestSighting(trackingData),
      location: this.getCurrentLocation(trackingData),
      health: this.getHealthMetrics(trackingData),
      pod: this.getPodInformation(trackingData),
      migration: this.getMigrationPattern(trackingData),
      photos: this.getRecentPhotos(trackingData)
    };
  }
  
  async calculateImpact(customerId: string): Promise<ImpactMetrics> {
    const purchases = await this.getPurchases(customerId);
    const donations = await this.getDonations(customerId);
    
    return {
      totalContributed: purchases.conservation + donations.amount,
      plasticRemoved: this.calculatePlasticRemoval(purchases),
      reefProtected: this.calculateReefArea(purchases),
      speciesHelped: this.getSpeciesImpact(purchases),
      co2Offset: this.calculateCarbonOffset(purchases)
    };
  }
}
```

## Virtual Tasting Platform

### WebRTC Implementation
```typescript
class VirtualTastingRoom {
  private agoraClient: AgoraRTC.Client;
  private localStream: AgoraRTC.Stream;
  
  async hostTasting(tastingId: string): Promise<void> {
    // Initialize Agora client
    this.agoraClient = AgoraRTC.createClient({ mode: 'live', codec: 'h264' });
    
    // Set host role
    await this.agoraClient.setClientRole('host');
    
    // Create local stream
    this.localStream = AgoraRTC.createStream({
      video: true,
      audio: true,
      screen: false
    });
    
    // Initialize and publish
    await this.localStream.init();
    await this.agoraClient.publish(this.localStream);
    
    // Enable interactive features
    this.enableChat();
    this.enablePolls();
    this.enableWineNotes();
    this.enableRecording();
  }
  
  async joinTasting(tastingId: string): Promise<void> {
    // Join as audience
    await this.agoraClient.setClientRole('audience');
    
    // Subscribe to host stream
    this.agoraClient.on('stream-added', async (evt) => {
      await this.agoraClient.subscribe(evt.stream);
      this.playStream(evt.stream);
    });
    
    // Enable participant features
    this.enableChat();
    this.enableReactions();
    this.enableQuestions();
  }
}
```

## API Architecture

### RESTful API Endpoints
```typescript
// Wine catalog
GET /api/wines
GET /api/wines/:id
GET /api/wines/search
POST /api/wines/recommend

// Subscriptions
GET /api/subscriptions/:customerId
POST /api/subscriptions/create
PUT /api/subscriptions/:id/preferences
POST /api/subscriptions/:id/skip

// Conservation
GET /api/conservation/impact/:customerId
POST /api/conservation/adopt
GET /api/conservation/tracking/:adoptionId
GET /api/conservation/leaderboard

// Virtual tastings
GET /api/tastings/upcoming
POST /api/tastings/register
GET /api/tastings/:id/stream
POST /api/tastings/:id/join
```

## Infrastructure

### Deployment Configuration
```yaml
# Docker Compose
version: '3.8'
services:
  web:
    image: azure-tide-wine-co/web:latest
    environment:
      - NEXT_PUBLIC_STRIPE_KEY=${STRIPE_KEY}
      - NEXT_PUBLIC_AGORA_APP_ID=${AGORA_APP_ID}
  
  api:
    image: azure-tide-wine-co/api:latest
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - STRIPE_SECRET=${STRIPE_SECRET}
  
  recommendation:
    image: azure-tide-wine-co/ml:latest
    deploy:
      resources:
        limits:
          memory: 4G
          cpus: '2'
  
  redis:
    image: redis:alpine
  
  postgres:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data
```

## Cost Analysis

### Monthly Infrastructure Costs
- E-commerce hosting: $200-400
- Databases: $150-300
- CDN (product images): $100-200
- Video streaming (tastings): $200-500
- ML compute: $100-300
- Blockchain fees: $50-100
- Email/SMS: $100-200
- **Total**: $900-2,000/month

---

*Azure Tide Wine CO's architecture combines luxury e-commerce with meaningful ocean conservation impact tracking and virtual wine experiences.*
