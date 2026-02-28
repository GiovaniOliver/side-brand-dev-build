# Technical Architecture - Brain Bender

## System Overview
Cognitive training and puzzle platform featuring AI-generated challenges, competitive leaderboards, educational content, and neuroscience-backed brain training exercises with gamification and social features.

## Technology Stack

### Frontend
**Framework**: Next.js 14 with TypeScript
```typescript
// Brain Training Platform Configuration
const PlatformConfig = {
  framework: 'Next.js 14',
  language: 'TypeScript',
  ui: {
    library: 'React 18',
    styling: 'Tailwind CSS + Stitches',
    components: 'Radix UI + Custom',
    animations: 'Framer Motion + Lottie',
    gameEngine: 'Phaser 3'
  },
  features: {
    ai: 'TensorFlow.js',
    analytics: 'Mixpanel',
    realtime: 'Socket.io',
    notifications: 'Web Push API',
    offline: 'Service Workers'
  }
};
```

**Core Components**:
- Puzzle game interfaces
- Progress tracking dashboard
- Multiplayer battle arena
- Learning management system
- Achievement system
- Social leaderboards
- Brain health metrics

### Backend Architecture

**Microservices Design**:
```javascript
const services = {
  puzzleService: {
    purpose: 'Puzzle generation and validation',
    tech: 'Python + FastAPI',
    ai: 'GPT-4 + Custom ML models',
    database: 'PostgreSQL',
    cache: 'Redis'
  },
  userProgressService: {
    purpose: 'Track cognitive improvement',
    tech: 'Node.js + Express',
    analytics: 'Custom algorithms',
    database: 'PostgreSQL + TimescaleDB',
    ml: 'scikit-learn'
  },
  multiplayerService: {
    purpose: 'Real-time competitive gaming',
    tech: 'Node.js + Socket.io',
    matching: 'ELO rating system',
    database: 'MongoDB',
    cache: 'Redis'
  },
  gamificationService: {
    purpose: 'Achievements and rewards',
    tech: 'Node.js + Express',
    features: ['Points', 'Badges', 'Streaks', 'Levels'],
    database: 'PostgreSQL'
  },
  contentService: {
    purpose: 'Educational content delivery',
    tech: 'Node.js + Strapi',
    storage: 'AWS S3',
    cdn: 'CloudFront'
  }
};
```

### Database Architecture

**PostgreSQL - Core Data**:
```sql
-- User profiles with cognitive tracking
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    age INTEGER,
    education_level VARCHAR(50),
    subscription_tier VARCHAR(20) DEFAULT 'free',
    cognitive_baseline JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Puzzle library
CREATE TABLE puzzles (
    id UUID PRIMARY KEY,
    type VARCHAR(50), -- logic, memory, attention, flexibility, speed
    difficulty INTEGER CHECK (difficulty >= 1 AND difficulty <= 10),
    category VARCHAR(100),
    title VARCHAR(255),
    instructions TEXT,
    puzzle_data JSONB,
    solution JSONB,
    time_limit INTEGER, -- seconds
    points INTEGER,
    cognitive_skills TEXT[],
    created_by VARCHAR(50), -- 'ai' or user_id
    play_count INTEGER DEFAULT 0,
    average_completion_time INTEGER,
    success_rate DECIMAL(5,2)
);

-- User progress tracking
CREATE TABLE user_progress (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    date DATE,
    cognitive_scores JSONB, -- {memory: 85, logic: 92, ...}
    puzzles_completed INTEGER,
    total_time_minutes INTEGER,
    streak_days INTEGER,
    brain_age INTEGER,
    improvement_rate DECIMAL(5,2)
);

-- Game sessions
CREATE TABLE game_sessions (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    puzzle_id UUID REFERENCES puzzles(id),
    started_at TIMESTAMP DEFAULT NOW(),
    completed_at TIMESTAMP,
    time_taken INTEGER, -- seconds
    score INTEGER,
    accuracy DECIMAL(5,2),
    hints_used INTEGER DEFAULT 0,
    cognitive_impact JSONB,
    device_type VARCHAR(50),
    session_metadata JSONB
);

-- Multiplayer battles
CREATE TABLE battles (
    id UUID PRIMARY KEY,
    player1_id UUID REFERENCES users(id),
    player2_id UUID REFERENCES users(id),
    puzzle_id UUID REFERENCES puzzles(id),
    winner_id UUID REFERENCES users(id),
    player1_score INTEGER,
    player2_score INTEGER,
    player1_time INTEGER,
    player2_time INTEGER,
    battle_type VARCHAR(50), -- quick, ranked, tournament
    elo_change INTEGER,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Achievements
CREATE TABLE achievements (
    id UUID PRIMARY KEY,
    name VARCHAR(100),
    description TEXT,
    category VARCHAR(50),
    points INTEGER,
    icon_url VARCHAR(255),
    unlock_criteria JSONB,
    rarity VARCHAR(20) -- common, rare, epic, legendary
);
```

**TimescaleDB - Analytics**:
```sql
-- Cognitive performance time series
CREATE TABLE cognitive_metrics (
    time TIMESTAMPTZ NOT NULL,
    user_id UUID NOT NULL,
    metric_type VARCHAR(50),
    value DECIMAL(10,2),
    puzzle_type VARCHAR(50),
    session_id UUID,
    PRIMARY KEY (time, user_id, metric_type)
);

-- Create hypertable for time-series data
SELECT create_hypertable('cognitive_metrics', 'time');

-- Continuous aggregates for performance tracking
CREATE MATERIALIZED VIEW daily_cognitive_scores
WITH (timescaledb.continuous) AS
SELECT 
    time_bucket('1 day', time) AS day,
    user_id,
    metric_type,
    AVG(value) as avg_score,
    MAX(value) as peak_score,
    COUNT(*) as sessions
FROM cognitive_metrics
GROUP BY day, user_id, metric_type;
```

## AI & Machine Learning

### Puzzle Generation Engine
```python
import openai
import numpy as np
from typing import Dict, List

class PuzzleGenerator:
    def __init__(self):
        self.gpt4 = openai.ChatCompletion()
        self.difficulty_model = self.load_difficulty_model()
        self.personalization_engine = PersonalizationEngine()
    
    async def generate_puzzle(self, user_profile: Dict, puzzle_type: str) -> Dict:
        """Generate personalized puzzle based on user's cognitive profile"""
        
        # Analyze user's performance history
        user_level = self.personalization_engine.get_user_level(user_profile)
        weak_areas = self.personalization_engine.identify_weak_areas(user_profile)
        
        # Generate puzzle with AI
        if puzzle_type == 'logic':
            puzzle = await self.generate_logic_puzzle(user_level)
        elif puzzle_type == 'memory':
            puzzle = await self.generate_memory_puzzle(user_level)
        elif puzzle_type == 'pattern':
            puzzle = await self.generate_pattern_puzzle(user_level)
        elif puzzle_type == 'word':
            puzzle = await self.generate_word_puzzle(user_level)
        
        # Adjust difficulty based on user profile
        puzzle = self.adjust_difficulty(puzzle, user_profile)
        
        # Add cognitive impact metadata
        puzzle['cognitive_impact'] = self.calculate_cognitive_impact(
            puzzle_type, 
            user_level,
            weak_areas
        )
        
        return puzzle
    
    async def generate_logic_puzzle(self, difficulty: int) -> Dict:
        prompt = f"""
        Generate a logic puzzle with difficulty {difficulty}/10.
        Include: problem statement, multiple choice answers, solution, explanation.
        Focus on: deductive reasoning, pattern recognition, problem solving.
        Format as JSON.
        """
        
        response = await self.gpt4.create(
            model="gpt-4",
            messages=[{"role": "system", "content": prompt}]
        )
        
        return self.parse_puzzle_response(response)
```

### Cognitive Assessment Engine
```python
class CognitiveAssessment:
    def __init__(self):
        self.metrics = {
            'memory': MemoryAnalyzer(),
            'attention': AttentionAnalyzer(),
            'flexibility': FlexibilityAnalyzer(),
            'speed': SpeedAnalyzer(),
            'problem_solving': ProblemSolvingAnalyzer()
        }
    
    def analyze_performance(self, session_data: Dict) -> Dict:
        """Analyze cognitive performance from game session"""
        
        results = {}
        
        # Calculate performance metrics
        for skill, analyzer in self.metrics.items():
            score = analyzer.calculate_score(session_data)
            improvement = analyzer.calculate_improvement(session_data)
            results[skill] = {
                'score': score,
                'improvement': improvement,
                'percentile': self.calculate_percentile(skill, score)
            }
        
        # Calculate brain age
        results['brain_age'] = self.calculate_brain_age(results)
        
        # Generate recommendations
        results['recommendations'] = self.generate_recommendations(results)
        
        return results
    
    def calculate_brain_age(self, metrics: Dict) -> int:
        """Calculate cognitive age based on performance"""
        weights = {
            'memory': 0.25,
            'attention': 0.20,
            'flexibility': 0.20,
            'speed': 0.20,
            'problem_solving': 0.15
        }
        
        weighted_score = sum(
            metrics[skill]['percentile'] * weight 
            for skill, weight in weights.items()
        )
        
        # Map percentile to brain age
        return self.percentile_to_brain_age(weighted_score)
```

## Gamification System

### Achievement & Progression
```typescript
class GamificationEngine {
  private achievements: Achievement[];
  private levels: Level[];
  
  async checkAchievements(userId: string, action: UserAction): Promise<Achievement[]> {
    const unlockedAchievements: Achievement[] = [];
    
    for (const achievement of this.achievements) {
      if (await this.isUnlocked(userId, achievement)) {
        continue;
      }
      
      if (this.checkCriteria(achievement.criteria, action)) {
        await this.unlockAchievement(userId, achievement);
        unlockedAchievements.push(achievement);
      }
    }
    
    return unlockedAchievements;
  }
  
  calculateLevel(experience: number): Level {
    // Exponential leveling curve
    const level = Math.floor(Math.sqrt(experience / 100));
    
    return {
      current: level,
      next: level + 1,
      experienceToNext: Math.pow(level + 1, 2) * 100 - experience,
      rewards: this.getLevelRewards(level + 1)
    };
  }
  
  async updateStreak(userId: string): Promise<StreakData> {
    const lastActivity = await this.getLastActivity(userId);
    const now = new Date();
    const daysSince = this.daysBetween(lastActivity, now);
    
    if (daysSince === 1) {
      // Continue streak
      return await this.incrementStreak(userId);
    } else if (daysSince > 1) {
      // Break streak
      return await this.resetStreak(userId);
    }
    
    return await this.getCurrentStreak(userId);
  }
}
```

## Multiplayer Architecture

### Real-time Battle System
```typescript
class MultiplayerBattle {
  private io: Server;
  private battles: Map<string, BattleSession>;
  
  async matchPlayers(player1: Player, player2: Player): Promise<BattleSession> {
    // Create battle session
    const battle = new BattleSession({
      players: [player1, player2],
      puzzle: await this.selectBalancedPuzzle(player1, player2),
      timeLimit: 180,
      mode: 'competitive'
    });
    
    // Setup real-time communication
    battle.on('answer', (playerId, answer) => {
      this.validateAnswer(battle, playerId, answer);
      this.broadcastUpdate(battle);
    });
    
    battle.on('complete', () => {
      this.calculateResults(battle);
      this.updateEloRatings(battle);
      this.awardRewards(battle);
    });
    
    return battle;
  }
  
  private calculateEloChange(winner: Player, loser: Player): number {
    const K = 32; // K-factor
    const expectedScore = 1 / (1 + Math.pow(10, (loser.elo - winner.elo) / 400));
    return Math.round(K * (1 - expectedScore));
  }
}
```

## API Architecture

### RESTful & WebSocket APIs
```typescript
// REST endpoints
GET /api/puzzles
GET /api/puzzles/:id
POST /api/puzzles/generate
GET /api/progress/daily
GET /api/progress/history
GET /api/leaderboard/:type

// Authenticated endpoints
GET /api/user/profile
PUT /api/user/preferences
GET /api/user/achievements
POST /api/sessions/start
POST /api/sessions/:id/complete

// WebSocket events
socket.on('join-battle', (data) => {})
socket.on('submit-answer', (data) => {})
socket.on('request-hint', (data) => {})
socket.on('challenge-friend', (data) => {})
```

## Performance Optimization

### Caching Strategy
```typescript
const CachingConfig = {
  redis: {
    puzzles: '1 hour',
    leaderboards: '5 minutes',
    userProgress: '10 minutes',
    achievements: '1 day'
  },
  cdn: {
    static: '1 year',
    images: '1 month',
    api: 'no-cache'
  },
  localStorage: {
    puzzleState: 'session',
    preferences: 'permanent',
    progress: '1 day'
  }
};
```

## Subscription & Monetization

### Tier System
```typescript
const SubscriptionTiers = {
  free: {
    puzzlesPerDay: 5,
    features: ['basic puzzles', 'progress tracking'],
    ads: true
  },
  premium: {
    price: 9.99,
    puzzlesPerDay: 'unlimited',
    features: ['all puzzles', 'detailed analytics', 'multiplayer', 'no ads'],
    ads: false
  },
  family: {
    price: 19.99,
    accounts: 5,
    features: ['everything in premium', 'family dashboard', 'parental controls'],
    ads: false
  }
};
```

## Infrastructure

### Deployment Architecture
```yaml
# Kubernetes deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: brain-bender
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: web
        image: brain-bender/web:latest
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
      - name: api
        image: brain-bender/api:latest
        resources:
          requests:
            memory: "1Gi"
            cpu: "1000m"
      - name: puzzle-engine
        image: brain-bender/puzzle-engine:latest
        resources:
          requests:
            memory: "2Gi"
            cpu: "2000m"
```

## Cost Analysis

### Monthly Infrastructure Costs
- Hosting (AWS/GCP): $300-600
- Databases: $200-400
- AI API (GPT-4): $500-1,500
- CDN: $100-200
- Analytics: $100-200
- Email/Push: $50-100
- **Total**: $1,250-3,000/month

---

*Brain Bender's architecture combines cognitive science with gamification to create an engaging and effective brain training platform.*