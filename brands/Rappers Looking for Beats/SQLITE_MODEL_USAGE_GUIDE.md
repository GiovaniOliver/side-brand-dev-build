# SQLite Model Usage Guide
## Rappers Looking for Beats - Quick Reference

## User Model Examples

### Create New User
```javascript
import User from './models/User.js';
import bcrypt from 'bcryptjs';

// Hash password first
const hashedPassword = await User.hashPassword('mypassword123');

// Create user
const user = User.create({
  username: 'cool_rapper',
  email: 'rapper@example.com',
  password: hashedPassword,
  userType: 'rapper',
  displayName: 'Cool Rapper',
  bio: 'Making fire tracks',
  location: {
    city: 'Los Angeles',
    state: 'CA',
    country: 'USA'
  }
});
```

### Find User
```javascript
// By ID
const user = User.findById(1);

// By Email
const user = User.findByEmail('rapper@example.com');

// By Username
const user = User.findByUsername('cool_rapper');

// All users with filters
const rappers = User.findAll({ 
  userType: 'rapper',
  isActive: true,
  limit: 10
});
```

### Update User
```javascript
const updated = User.update(userId, {
  bio: 'New bio text',
  displayName: 'New Name'
});
```

### Login / Password Check
```javascript
const user = User.findByEmail(email);
if (user) {
  const isMatch = await User.comparePassword(password, user.password);
  if (isMatch) {
    // Login successful
  }
}
```

### Artist Info
```javascript
// Save artist info
User.saveArtistInfo(userId, {
  genres: ['trap', 'melodic-rap'],
  influences: ['Drake', 'Travis Scott'],
  yearsActive: 3,
  lookingFor: 'Trap beats with melodic elements'
});

// Get artist info
const artistInfo = User.getArtistInfo(userId);
```

### Producer Info
```javascript
// Save producer info
User.saveProducerInfo(userId, {
  stageName: 'Beat Master',
  specialties: ['trap', 'drill'],
  equipment: ['FL Studio', 'Ableton', 'Komplete'],
  yearsProducing: 5,
  productionStyle: 'Dark, hard-hitting beats'
});

// Get producer info
const producerInfo = User.getProducerInfo(userId);
```

### Demo Tracks
```javascript
// Add demo track
User.addDemoTrack(userId, {
  title: 'My Latest Track',
  url: 'https://soundcloud.com/...'
});

// Get all demo tracks
const demos = User.getDemoTracks(userId);
```

### Favorites
```javascript
// Add favorite beat
User.addFavoriteBeat(userId, beatId);

// Remove favorite beat
User.removeFavoriteBeat(userId, beatId);

// Get favorite beats
const favoriteBeats = User.getFavoriteBeats(userId);

// Add favorite producer
User.addFavoriteProducer(userId, producerId);

// Get favorite producers
const favoriteProducers = User.getFavoriteProducers(userId);
```

### Search Users
```javascript
const results = User.search('producer name', 'producer');
```

---

## Beat Model Examples

### Create New Beat
```javascript
import Beat from './models/Beat.js';

const beat = Beat.create({
  title: 'Dark Trap Beat',
  description: 'Hard-hitting 808s with dark melodies',
  producerId: userId,
  audioFile: {
    url: 'https://cdn.example.com/beat.mp3',
    filename: 'dark-trap-beat.mp3',
    size: 5242880,
    duration: 180,
    format: 'mp3'
  },
  previewFile: {
    url: 'https://cdn.example.com/preview.mp3',
    duration: 30
  },
  coverImage: 'beat-cover.jpg',
  genre: 'trap',
  subGenres: ['dark-trap', 'aggressive'],
  bpm: 140,
  key: 'G',
  scale: 'minor',
  mood: ['dark', 'aggressive', 'energetic'],
  tags: ['808', 'trap', 'hard'],
  pricing: {
    free: false,
    basicLease: {
      available: true,
      price: 29.99
    },
    premiumLease: {
      available: true,
      price: 49.99
    },
    exclusiveRights: {
      available: true,
      price: 299.99
    }
  }
});
```

### Find Beats
```javascript
// By ID
const beat = Beat.findById(beatId);

// All beats with filters
const beats = Beat.findAll({
  producerId: userId,
  genre: 'trap',
  isActive: true,
  isFree: false,
  bpmMin: 120,
  bpmMax: 160,
  sortBy: 'newest', // 'plays', 'rating', 'popular', 'newest'
  limit: 20,
  offset: 0
});
```

### Update Beat
```javascript
Beat.update(beatId, {
  title: 'New Title',
  pricing: {
    basicLease: {
      price: 24.99
    }
  }
});
```

### Track Beat Activity
```javascript
// Increment plays
Beat.incrementPlays(beatId);

// Increment downloads
Beat.incrementDownloads(beatId);

// Add to favorites
Beat.addFavorite(beatId);

// Remove from favorites
Beat.removeFavorite(beatId);
```

### Record Sale
```javascript
// Records sale and updates producer earnings
Beat.recordSale(beatId, 29.99, 'basic');

// For exclusive rights (marks beat as sold)
Beat.recordSale(beatId, 299.99, 'exclusive');
```

### Search Beats
```javascript
const results = Beat.search('dark trap', {
  genre: 'trap'
});
```

### Get Featured/Trending Beats
```javascript
// Get featured beats
const featured = Beat.getFeatured(10);

// Get trending beats
const trending = Beat.getTrending(10);
```

### Get Producer's Beat Count
```javascript
const count = Beat.getCountByProducer(producerId);
```

---

## Common Route Patterns

### Authentication Route
```javascript
// routes/auth.js
import User from '../models/User.js';
import jwt from 'jsonwebtoken';

// Register
router.post('/register', async (req, res) => {
  try {
    const { username, email, password, userType } = req.body;
    
    // Check if user exists
    if (User.findByEmail(email)) {
      return res.status(400).json({ message: 'Email already exists' });
    }
    
    // Hash password
    const hashedPassword = await User.hashPassword(password);
    
    // Create user
    const user = User.create({
      username,
      email,
      password: hashedPassword,
      userType
    });
    
    // Generate token
    const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET);
    
    res.status(201).json({ token, user: User.getPublicProfile(user.id) });
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});

// Login
router.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    const user = User.findByEmail(email);
    
    if (!user) {
      return res.status(401).json({ message: 'Invalid credentials' });
    }
    
    const isMatch = await User.comparePassword(password, user.password);
    
    if (!isMatch) {
      return res.status(401).json({ message: 'Invalid credentials' });
    }
    
    const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET);
    
    res.json({ token, user: User.getPublicProfile(user.id) });
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});
```

### Beats Route
```javascript
// routes/beats.js
import Beat from '../models/Beat.js';

// Get all beats
router.get('/', (req, res) => {
  try {
    const { genre, bpmMin, bpmMax, sortBy, limit, offset } = req.query;
    
    const beats = Beat.findAll({
      genre,
      bpmMin: bpmMin ? parseInt(bpmMin) : undefined,
      bpmMax: bpmMax ? parseInt(bpmMax) : undefined,
      sortBy,
      limit: limit ? parseInt(limit) : 20,
      offset: offset ? parseInt(offset) : 0,
      isActive: true
    });
    
    res.json(beats);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});

// Get beat by ID
router.get('/:id', (req, res) => {
  try {
    const beat = Beat.findById(req.params.id);
    
    if (!beat) {
      return res.status(404).json({ message: 'Beat not found' });
    }
    
    // Increment play count
    Beat.incrementPlays(beat.id);
    
    res.json(beat);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});

// Create beat (requires auth)
router.post('/', authMiddleware, async (req, res) => {
  try {
    const beat = Beat.create({
      ...req.body,
      producerId: req.user.id
    });
    
    res.status(201).json(beat);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});
```

---

## Database Initialization

The database is automatically initialized when the server starts. Tables are created if they don't exist.

### Manual Database Reset
```javascript
import { db } from './config/database.js';

// Drop all tables
db.exec(`
  DROP TABLE IF EXISTS transactions;
  DROP TABLE IF EXISTS messages;
  DROP TABLE IF EXISTS reviews;
  DROP TABLE IF EXISTS favorites;
  DROP TABLE IF EXISTS beats;
  DROP TABLE IF EXISTS producer_info;
  DROP TABLE IF EXISTS demo_tracks;
  DROP TABLE IF EXISTS artist_info;
  DROP TABLE IF EXISTS users;
`);

// Restart server to recreate tables
```

---

## Tips & Best Practices

### 1. Always Use Prepared Statements
The models already use prepared statements for security. Never concatenate user input into SQL queries.

### 2. JSON Fields
Some fields like `genres`, `tags`, `mood` are stored as JSON strings. The models handle parsing automatically:
```javascript
// Stored as: '["trap","drill"]'
// Returned as: ["trap", "drill"]
```

### 3. Error Handling
Wrap database operations in try-catch blocks:
```javascript
try {
  const user = User.create(userData);
} catch (error) {
  console.error('Database error:', error);
  // Handle unique constraint violations, etc.
}
```

### 4. Transactions (if needed)
For operations that need to be atomic:
```javascript
import { db } from './config/database.js';

const transaction = db.transaction((userId, beatId, amount) => {
  // Record sale
  Beat.recordSale(beatId, amount, 'basic');
  
  // Update user
  User.update(userId, { /* ... */ });
});

transaction(userId, beatId, amount);
```

### 5. Indexes
All common query patterns are indexed. If you add new query patterns, consider adding indexes:
```javascript
db.exec(`CREATE INDEX IF NOT EXISTS idx_custom ON table_name(column_name)`);
```

---

## Migration Notes

### Differences from MongoDB

| Feature | MongoDB | SQLite |
|---------|---------|--------|
| ID Type | ObjectId | Integer (auto-increment) |
| Queries | `.find()` | `Model.findAll()` |
| Create | `new Model().save()` | `Model.create()` |
| Update | `.save()` | `Model.update(id, data)` |
| Relations | Populate | SQL JOINs (handled in models) |
| Arrays | Native | JSON strings |

### What Stays the Same
- Password hashing (bcrypt)
- JWT authentication
- File uploads (S3/Cloudinary)
- Socket.io messaging
- Stripe payments
- All business logic

---

**Last Updated:** $(date +%Y-%m-%d)
**Database:** SQLite with better-sqlite3
**Models:** User, Beat
