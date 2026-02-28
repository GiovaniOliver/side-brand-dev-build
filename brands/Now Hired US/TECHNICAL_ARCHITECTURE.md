# Now Hired US - Technical Architecture

## Overview

This document outlines the technical architecture for the Now Hired US platform ecosystem, focusing on AI-powered job placement, candidate matching, and comprehensive recruitment management systems.

## Core Technology Stack

### Frontend Framework
- **Next.js 14** with App Router
  - Server Components for optimal job board performance and SEO
  - Static Site Generation for job listings and career resources
  - Built-in image optimization for company profiles and candidate photos
  - TypeScript for reliable data handling and form management

### Styling & UI
- **Tailwind CSS** with professional corporate design system
- **Responsive Design** optimized for both desktop and mobile recruiting
- **Component Library** for consistent recruitment interface elements
- **Headless UI** for accessible form components and modals

### Database & Storage
- **PostgreSQL** for complex relational data (jobs, candidates, placements)
- **Redis** for session management and real-time matching cache
- **AWS S3** for resume storage, company documents, and candidate portfolios
- **Elasticsearch** for advanced job and candidate search capabilities

### Authentication & Security
- **NextAuth.js** with role-based access (employers, candidates, recruiters)
- **HTTPS enforcement** across all recruitment platform interfaces
- **GDPR/CCPA compliance** for candidate and employer data protection
- **Background check API integration** with secure data handling

### AI and Machine Learning
- **OpenAI GPT-4** for resume parsing and job description optimization
- **Custom ML models** for candidate-job matching algorithms
- **Skills assessment integration** with automated scoring systems
- **Predictive analytics** for placement success and retention forecasting

### Payment Processing
- **Stripe** for placement fee billing and automated invoicing
- **ACH transfers** for high-value staffing transactions
- **Automated billing** based on successful placement confirmations
- **Multi-currency support** for international placements

### Communication Systems
- **Twilio** for SMS notifications and interview reminders
- **SendGrid** for transactional emails and recruitment campaigns
- **Calendly API** for interview scheduling automation
- **Zoom API** for virtual interview integration

## Application Architecture

### Monorepo Structure
```
now-hired-us-platform/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── (marketing)/       # Public website and landing pages
│   │   ├── (employers)/       # Employer dashboard and job posting
│   │   ├── (candidates)/      # Candidate portal and applications
│   │   ├── (recruiters)/      # Internal recruiter tools
│   │   ├── (admin)/           # Administrative panel
│   │   └── api/               # API routes and integrations
│   ├── components/            # Shared UI components
│   │   ├── ui/               # Base design system components
│   │   ├── forms/            # Job posting and application forms
│   │   ├── matching/         # AI matching interface components
│   │   └── dashboard/        # Analytics and reporting components
│   ├── lib/                  # Utility libraries
│   │   ├── auth.ts           # Authentication and authorization
│   │   ├── db.ts             # Database connections and queries
│   │   ├── ai-matching.ts    # AI candidate matching algorithms
│   │   ├── billing.ts        # Payment processing and invoicing
│   │   ├── communications.ts # Email and SMS automation
│   │   └── analytics.ts      # Performance tracking and reporting
│   └── types/                # TypeScript definitions
├── docs/                     # API and system documentation
├── ml-models/                # Custom machine learning models
└── integrations/            # Third-party API integrations
```

### Database Schema Design

#### Jobs Table
```sql
CREATE TABLE jobs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  employer_id UUID NOT NULL REFERENCES employers(id),
  title VARCHAR(255) NOT NULL,
  description TEXT NOT NULL,
  requirements TEXT NOT NULL,
  salary_min INTEGER,
  salary_max INTEGER,
  location VARCHAR(255),
  remote_option BOOLEAN DEFAULT FALSE,
  employment_type VARCHAR(50) NOT NULL,
  skills_required JSONB,
  experience_level VARCHAR(50),
  posted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP,
  status VARCHAR(50) DEFAULT 'active',
  recruiter_id UUID REFERENCES recruiters(id)
);
```

#### Candidates Table
```sql
CREATE TABLE candidates (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  phone VARCHAR(20),
  location VARCHAR(255),
  desired_salary_min INTEGER,
  desired_salary_max INTEGER,
  skills JSONB,
  experience_years INTEGER,
  education_level VARCHAR(100),
  resume_url VARCHAR(500),
  linkedin_url VARCHAR(500),
  availability_date DATE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Applications Table
```sql
CREATE TABLE applications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  job_id UUID NOT NULL REFERENCES jobs(id),
  candidate_id UUID NOT NULL REFERENCES candidates(id),
  submitted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  status VARCHAR(50) DEFAULT 'pending',
  recruiter_notes TEXT,
  match_score DECIMAL(3,2),
  interview_scheduled_at TIMESTAMP,
  offer_amount INTEGER,
  placement_date DATE,
  retention_90_day BOOLEAN
);
```

#### Placements Table
```sql
CREATE TABLE placements (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  job_id UUID NOT NULL REFERENCES jobs(id),
  candidate_id UUID NOT NULL REFERENCES candidates(id),
  employer_id UUID NOT NULL REFERENCES employers(id),
  recruiter_id UUID NOT NULL REFERENCES recruiters(id),
  placement_date DATE NOT NULL,
  start_date DATE NOT NULL,
  salary INTEGER NOT NULL,
  placement_fee DECIMAL(10,2) NOT NULL,
  fee_percentage DECIMAL(4,2) NOT NULL,
  guarantee_period INTEGER DEFAULT 90,
  status VARCHAR(50) DEFAULT 'active',
  retention_30_day BOOLEAN,
  retention_60_day BOOLEAN,
  retention_90_day BOOLEAN
);
```

### API Routes Structure

#### Core Recruitment Routes
- `/api/jobs` - Job posting and management
- `/api/candidates` - Candidate profiles and applications
- `/api/matching` - AI-powered candidate-job matching
- `/api/applications` - Application tracking and management
- `/api/placements` - Successful placement recording and tracking

#### AI and Analytics Routes
- `/api/ai/parse-resume` - Resume parsing and skills extraction
- `/api/ai/job-optimization` - Job description optimization suggestions
- `/api/ai/match-candidates` - Candidate matching algorithm
- `/api/analytics/dashboard` - Performance metrics and reporting
- `/api/analytics/retention` - Placement retention analysis

#### Business Operations Routes
- `/api/billing/invoices` - Automated billing and invoicing
- `/api/communications/interviews` - Interview scheduling automation  
- `/api/integrations/background-checks` - Background verification
- `/api/reporting/compliance` - EEOC and legal compliance reporting

## AI Matching Algorithm Architecture

### Skills-Based Matching System
```typescript
interface MatchingEngine {
  skillsAnalysis: SkillsExtractionService;
  experienceWeighting: ExperienceCalculator;
  locationPreferences: GeographicMatcher;
  salaryCompatibility: CompensationAnalyzer;
  culturalFit: CompanyMatcher;
  careerGrowth: ProgressionPredictor;
}
```

### Machine Learning Models
- **Resume Parser**: Extract skills, experience, and qualifications from resumes
- **Job-Candidate Compatibility**: Predict successful placement probability
- **Retention Predictor**: Forecast 90-day retention likelihood
- **Salary Benchmarking**: Market-based compensation recommendations
- **Interview Success Prediction**: Optimize candidate-employer matching

### Real-time Matching Process
1. **Job Analysis**: Parse job requirements and extract key criteria
2. **Candidate Scoring**: Score all candidates against job requirements  
3. **Ranking Algorithm**: Prioritize candidates by match score and availability
4. **48-Hour Commitment**: Automated submission of top candidates within deadline
5. **Feedback Loop**: Improve matching based on interview and placement outcomes

## Security & Performance

### Data Security
- **Encryption at Rest**: All candidate and employer data encrypted
- **GDPR Compliance**: Full data protection and right-to-deletion support
- **Background Check Security**: Secure API integration with verification services
- **Role-Based Access**: Strict permissions for recruiters, employers, and candidates
- **Audit Logging**: Complete tracking of all data access and modifications

### Performance Optimization
- **Database Indexing**: Optimized queries for job search and candidate matching
- **Caching Strategy**: Redis caching for frequently accessed job and candidate data
- **CDN Integration**: Fast resume and document delivery globally
- **Load Balancing**: Scalable architecture for high-volume recruitment periods
- **API Rate Limiting**: Protection against abuse and system overload

### Monitoring & Analytics
- **Placement Success Tracking**: Monitor 30, 60, and 90-day retention rates
- **Revenue Analytics**: Track placement fees and billing performance
- **Recruiter Performance**: Individual and team productivity metrics
- **Client Satisfaction**: NPS tracking and feedback analysis
- **System Performance**: Uptime monitoring and error tracking

## Integration Ecosystem

### Job Board Integrations
- **Indeed**: Automated job posting and candidate sourcing
- **LinkedIn**: Professional network integration and candidate outreach
- **Monster**: Traditional job board posting and application management
- **CareerBuilder**: Corporate client integration and bulk posting
- **Glassdoor**: Company review integration and employer branding

### Background Check Services
- **Sterling**: Comprehensive background verification services
- **First Advantage**: Employment and education verification
- **HireRight**: Criminal background and reference checks
- **Accurate**: Identity verification and drug screening coordination

### Communication Platforms
- **Microsoft Teams**: Enterprise client communication integration
- **Slack**: Startup and tech company communication channels
- **Calendly**: Interview scheduling automation
- **Zoom**: Virtual interview platform integration

## Deployment Strategy

### Production Environment
- **AWS ECS**: Containerized application deployment with auto-scaling
- **RDS PostgreSQL**: Managed database with automated backups
- **ElastiCache**: Redis caching layer for improved performance
- **CloudFront**: Global CDN for resume and document delivery
- **Route 53**: DNS management with health checking

### CI/CD Pipeline
- **GitHub Actions**: Automated testing and deployment workflows
- **Docker**: Containerization for consistent deployment environments
- **Terraform**: Infrastructure as code for environment management
- **Monitoring**: Comprehensive logging and performance monitoring

## Business Intelligence

### Recruitment Analytics
- **Placement Success Rates**: Track placement success by recruiter, industry, and role type
- **Time-to-Fill Metrics**: Monitor recruitment efficiency and client satisfaction
- **Revenue Attribution**: Connect placements to revenue and profitability analysis
- **Market Intelligence**: Salary trends and hiring demand forecasting

### Predictive Analytics
- **Retention Forecasting**: Predict long-term placement success
- **Client Lifetime Value**: Calculate and optimize client relationship profitability
- **Demand Forecasting**: Anticipate hiring trends and market opportunities
- **Candidate Success Prediction**: Identify high-potential candidates for priority roles

---

*This technical architecture ensures Now Hired US delivers efficient, secure, and scalable recruitment services while maintaining competitive advantage through AI-powered matching and comprehensive business intelligence.*