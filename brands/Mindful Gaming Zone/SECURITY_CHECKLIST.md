# SECURITY CHECKLIST - Mindful Gaming Zone
## Quick Reference for Production Deployment

**Date**: January 2024
**Status**: Pre-Production Setup Required

---

## ✅ IMPLEMENTATION STATUS

All security features have been implemented. Use this checklist to prepare for production deployment.

### Security Features Completed

- [x] **Zod Validation** - 20+ schemas for all data models
- [x] **IP Anonymization** - SHA-256 hashing with configurable salt
- [x] **Rate Limiting** - Redis-backed distributed limiting
- [x] **XSS Protection** - DOMPurify + CSP + security headers
- [x] **CSRF Protection** - Origin validation + token-based
- [x] **SQL Injection Prevention** - Parameterized query examples
- [x] **Authentication** - Bcrypt + JWT implementation
- [x] **Error Handling** - Safe error responses
- [x] **Documentation** - 2,400+ lines of comprehensive docs

---

## 🚀 PRE-PRODUCTION CHECKLIST

### Step 1: Environment Configuration (Required)

**Time Required**: 15 minutes

#### Generate Secrets

Run these commands to generate secure secrets:

```bash
# JWT Secret
echo "JWT_SECRET=$(openssl rand -base64 32)"

# IP Hash Salt
echo "IP_HASH_SALT=$(openssl rand -base64 32)"

# NextAuth Secret
echo "NEXTAUTH_SECRET=$(openssl rand -base64 32)"

# Database Encryption Key
echo "DATABASE_ENCRYPTION_KEY=$(openssl rand -hex 32)"
```

#### Configure .env File

Copy `.env.example` to `.env` and set these REQUIRED values:

```bash
cd website
cp .env.example .env
```

**Edit `.env` and set:**

- [ ] `NODE_ENV=production`
- [ ] `NEXT_PUBLIC_APP_URL=https://your-domain.com`
- [ ] `DATABASE_URL=postgresql://user:pass@host:5432/db?sslmode=require`
- [ ] `JWT_SECRET=<generated-secret-from-above>`
- [ ] `IP_HASH_SALT=<generated-salt-from-above>`
- [ ] `NEXTAUTH_SECRET=<generated-secret-from-above>`
- [ ] `REDIS_URL=redis://user:pass@host:6379` (for distributed rate limiting)
- [ ] `FORCE_HTTPS=true`
- [ ] `ALLOWED_ORIGINS=https://your-domain.com`

**Verify all secrets are:**
- [ ] Unique (not from examples)
- [ ] Strong (32+ characters)
- [ ] Not hardcoded in code
- [ ] Stored securely (not in version control)

---

### Step 2: Security Configuration Review (Required)

**Time Required**: 10 minutes

#### Content Security Policy

Edit `src/middleware/security.ts` and update CSP directives:

- [ ] Remove `'unsafe-eval'` from `script-src` (production only)
- [ ] Update `script-src` with your CDN domains
- [ ] Update `connect-src` with your API domain
- [ ] Update `img-src` with your image CDN
- [ ] Update `media-src` with your media CDN

#### CORS Configuration

- [ ] Set `ALLOWED_ORIGINS` to your frontend domains only
- [ ] Verify `TRUST_PROXY=true` only if behind reverse proxy
- [ ] Set `ALLOW_CREDENTIALS=true` if using cookies

#### Database Configuration

- [ ] Database connection uses SSL (`?sslmode=require`)
- [ ] Database user has minimum required privileges
- [ ] Database password is strong (20+ characters)

---

### Step 3: Redis Setup (Highly Recommended)

**Time Required**: 15 minutes

**Why**: Distributed rate limiting across multiple server instances

#### Option A: Managed Redis (Recommended)

Use a managed Redis service:
- **Vercel**: Upstash Redis
- **AWS**: ElastiCache
- **DigitalOcean**: Managed Redis
- **Redis Cloud**: redis.com

Set in `.env`:
```bash
REDIS_URL=redis://user:password@your-redis-host:6379
```

#### Option B: Self-Hosted Redis

Install and configure Redis, then set:
```bash
REDIS_HOST=your-redis-host
REDIS_PORT=6379
REDIS_PASSWORD=your-redis-password
```

#### Verify Redis Connection

- [ ] Redis is accessible from your application
- [ ] Redis requires authentication
- [ ] Redis uses TLS if over public network
- [ ] Redis has backup/persistence enabled

**Note**: Without Redis, rate limiting uses in-memory storage (not suitable for multi-instance deployments).

---

### Step 4: Install Dependencies (Required)

**Time Required**: 5 minutes

```bash
cd website
npm install
```

#### Verify Installation

- [ ] No critical vulnerabilities: `npm audit`
- [ ] All dependencies installed: `ls node_modules`
- [ ] TypeScript builds: `npm run type-check`

---

### Step 5: Security Audit (Required)

**Time Required**: 10 minutes

#### Run Security Checks

```bash
# Check for vulnerabilities
npm audit

# Fix critical issues
npm audit fix

# Check for outdated packages
npm outdated

# TypeScript type checking
npm run type-check

# Lint code
npm run lint
```

#### Verify Results

- [ ] No HIGH or CRITICAL vulnerabilities
- [ ] TypeScript compiles without errors
- [ ] No linting errors
- [ ] All security packages up to date:
  - `zod` (latest)
  - `rate-limiter-flexible` (latest)
  - `bcryptjs` (latest)
  - `dompurify` / `isomorphic-dompurify` (latest)

---

### Step 6: Test Security Features (Required)

**Time Required**: 15 minutes

#### Test Rate Limiting

```bash
# Test login rate limit (should block after 5 attempts)
for i in {1..6}; do
  curl -X POST http://localhost:3000/api/auth/login \
    -H "Content-Type: application/json" \
    -d '{"email":"test@test.com","password":"test"}' \
    -w "\nStatus: %{http_code}\n"
done
```

**Expected**: First 5 return 401, 6th returns 429 (Rate Limit Exceeded)

- [ ] Rate limiting works on login endpoint
- [ ] 429 response includes `Retry-After` header
- [ ] Error message is generic

#### Test Input Validation

```bash
# Test validation (should fail)
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"ab","email":"invalid","password":"short"}' \
  -w "\nStatus: %{http_code}\n"
```

**Expected**: 400 Bad Request with validation errors

- [ ] Validation rejects invalid data
- [ ] Error messages are descriptive
- [ ] No stack traces in response

#### Test Security Headers

```bash
# Check security headers
curl -I http://localhost:3000/api/wellness
```

**Expected headers:**
- `Content-Security-Policy: ...`
- `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`
- `X-Frame-Options: DENY`
- `X-Content-Type-Options: nosniff`
- `X-XSS-Protection: 1; mode=block`

- [ ] All security headers present
- [ ] CSP header correctly configured
- [ ] HSTS header includes preload

#### Test IP Anonymization

Check server logs for anonymized IP hashes:

- [ ] IPs are hashed (64-character hex strings)
- [ ] Same IP produces same hash (consistency)
- [ ] Original IPs not logged anywhere

---

### Step 7: Build and Deploy (Required)

**Time Required**: 10 minutes

#### Build for Production

```bash
npm run build
```

- [ ] Build completes without errors
- [ ] No security warnings in build output
- [ ] Static analysis passes

#### Deploy to Hosting

Choose your platform:
- **Vercel**: `vercel --prod`
- **AWS**: Deploy to Elastic Beanstalk / ECS / Lambda
- **DigitalOcean**: App Platform
- **Custom**: Docker container

#### Post-Deployment Verification

- [ ] HTTPS is enforced (HTTP redirects to HTTPS)
- [ ] Domain certificate is valid
- [ ] Security headers present in production
- [ ] Rate limiting works in production
- [ ] API endpoints return expected responses

---

### Step 8: Monitoring Setup (Highly Recommended)

**Time Required**: 20 minutes

#### Error Tracking (Sentry)

1. Sign up at sentry.io
2. Create a new project
3. Add to `.env`:
   ```bash
   SENTRY_DSN=https://your-dsn@sentry.io/project-id
   SENTRY_AUTH_TOKEN=your-auth-token
   ```
4. Verify errors are captured

- [ ] Sentry configured
- [ ] Test error captured successfully
- [ ] Source maps uploaded (for stack traces)

#### Logging

Configure log aggregation:
- **CloudWatch** (AWS)
- **Datadog**
- **Logtail**
- **Papertrail**

- [ ] Logs are centralized
- [ ] Sensitive data not logged (passwords, tokens)
- [ ] Anonymized IPs logged
- [ ] Log retention policy set (90 days)

#### Uptime Monitoring

Set up monitoring:
- **UptimeRobot** (free)
- **Pingdom**
- **StatusCake**
- **Better Uptime**

- [ ] Uptime monitoring configured
- [ ] Alerts configured (email/SMS/Slack)
- [ ] Health check endpoint monitored

#### Security Alerts

Set up alerts for:
- [ ] Multiple failed login attempts (>10 in 5 minutes)
- [ ] Rate limit violations (>100 in 1 hour)
- [ ] Database errors
- [ ] Authentication errors
- [ ] Unusual traffic patterns

---

### Step 9: Backup Configuration (Required)

**Time Required**: 15 minutes

#### Database Backups

- [ ] Automated daily backups enabled
- [ ] Backups encrypted at rest
- [ ] Backup retention: 30 days minimum
- [ ] Backup restoration tested (critical!)

#### Redis Backups (if using persistence)

- [ ] RDB snapshots enabled
- [ ] AOF persistence configured
- [ ] Backup location secure

#### Code Backups

- [ ] Git repository has backups
- [ ] Secrets stored separately (not in repo)
- [ ] Environment variables documented

---

### Step 10: Security Documentation (Required)

**Time Required**: 10 minutes

#### Internal Documentation

- [ ] Security runbook created (incident response)
- [ ] Team trained on security features
- [ ] Deployment checklist reviewed
- [ ] Contact information updated (security team, emergency)

#### External Documentation

- [ ] Privacy Policy updated
- [ ] Terms of Service updated
- [ ] Security page created (/security)
- [ ] Responsible disclosure policy published

---

## 📋 ONGOING SECURITY TASKS

### Daily

- [ ] Review error logs for anomalies
- [ ] Check failed login attempts
- [ ] Monitor rate limit violations

### Weekly

- [ ] Review access logs
- [ ] Check for unusual API usage
- [ ] Verify backup completion
- [ ] Review security alerts

### Monthly

- [ ] Run `npm audit` and update dependencies
- [ ] Review user permissions
- [ ] Check for new security advisories
- [ ] Update security documentation

### Quarterly (Every 90 Days)

- [ ] Rotate secrets (JWT_SECRET, IP_HASH_SALT)
- [ ] Full security audit
- [ ] Penetration testing (internal)
- [ ] Security training for team
- [ ] Review and update threat model

### Annually

- [ ] Third-party security audit / penetration testing
- [ ] Update security policies
- [ ] Compliance audit (GDPR, etc.)
- [ ] Update incident response plan
- [ ] Review and update this checklist

---

## 🔒 SECURITY VALIDATION

### Pre-Launch Validation

Run through this checklist before launching:

#### Environment
- [ ] `NODE_ENV=production`
- [ ] `FORCE_HTTPS=true`
- [ ] All secrets are strong and unique
- [ ] No default/example secrets in use
- [ ] `.env` file not in version control

#### Security Features
- [ ] Rate limiting tested and working
- [ ] Input validation rejecting invalid data
- [ ] Security headers present on all responses
- [ ] IP anonymization producing hashes
- [ ] CSRF protection enabled on state-changing endpoints
- [ ] Authentication working correctly
- [ ] Authorization checks on protected endpoints

#### Database
- [ ] SSL/TLS enabled
- [ ] Least privilege database user
- [ ] Parameterized queries used (no string concatenation)
- [ ] Backups automated and tested

#### Code Quality
- [ ] No hardcoded secrets
- [ ] No console.log in production code
- [ ] No TODO security items
- [ ] TypeScript strict mode enabled
- [ ] All security functions properly imported

#### Monitoring
- [ ] Error tracking configured
- [ ] Logging configured
- [ ] Uptime monitoring configured
- [ ] Security alerts configured

#### Documentation
- [ ] Security documentation complete
- [ ] Team trained on security features
- [ ] Incident response plan documented
- [ ] Contact information current

---

## 🆘 SECURITY INCIDENT RESPONSE

### If a Security Issue is Discovered

1. **Contain**: Isolate affected systems immediately
2. **Investigate**: Review logs, identify scope
3. **Notify**: Contact security team (security@mindful-gaming-zone.com)
4. **Fix**: Apply security patches
5. **Verify**: Test fix thoroughly
6. **Document**: Record incident details
7. **Learn**: Update security measures

### Emergency Contacts

- **Security Team**: security@mindful-gaming-zone.com
- **Emergency**: [Add phone number]
- **Hosting Provider Support**: [Add contact]
- **Database Provider Support**: [Add contact]

---

## 📚 RESOURCES

### Documentation
- **SECURITY.md** - Complete security guide (1,000+ lines)
- **QUICKSTART.md** - 5-minute setup guide
- **README.md** - Project overview
- **SECURITY_IMPLEMENTATION_REPORT.md** - Detailed audit
- **SECURITY_FIX_SUMMARY.md** - Executive summary

### External Resources
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)
- [Next.js Security Best Practices](https://nextjs.org/docs/app/building-your-application/configuring/security)
- [npm Security Best Practices](https://docs.npmjs.com/security-best-practices)

### Security Tools
- `npm audit` - Dependency vulnerability scanner
- [Snyk](https://snyk.io/) - Continuous security monitoring
- [OWASP ZAP](https://www.zaproxy.org/) - Security scanner
- [Burp Suite](https://portswigger.net/burp) - Security testing

---

## ✅ FINAL CHECKLIST

Before going live, ensure ALL items are checked:

### Critical (Must Complete)

- [ ] All secrets generated and configured
- [ ] `NODE_ENV=production`
- [ ] `FORCE_HTTPS=true`
- [ ] Database SSL enabled
- [ ] Redis configured (for distributed rate limiting)
- [ ] Security headers verified
- [ ] Rate limiting tested
- [ ] Input validation tested
- [ ] `npm audit` shows no critical issues
- [ ] Backups configured and tested
- [ ] Monitoring configured
- [ ] HTTPS certificate valid

### Highly Recommended

- [ ] Sentry error tracking configured
- [ ] Uptime monitoring configured
- [ ] Security alerts configured
- [ ] Team trained on security
- [ ] Documentation complete
- [ ] Incident response plan documented

### Optional (But Recommended)

- [ ] Third-party security audit completed
- [ ] Load testing completed
- [ ] Disaster recovery plan documented
- [ ] Compliance certification (if required)

---

## 📊 SECURITY SCORE

Track your security posture:

**Total Checklist Items**: 150+

**Your Score**: ___ / 150

**Grade**:
- 140-150: A+ (Excellent)
- 130-139: A (Very Good)
- 120-129: B (Good)
- 110-119: C (Acceptable)
- Below 110: Needs Improvement

**Recommendation**: Aim for at least 140/150 before production launch.

---

## 🎯 QUICK REFERENCE

### Most Important Security Measures

1. **Strong Secrets** - 32+ character random strings
2. **Rate Limiting** - Prevent brute force and DDoS
3. **Input Validation** - Reject invalid/malicious data
4. **HTTPS** - Encrypt all traffic
5. **Secure Headers** - Prevent common attacks
6. **Backups** - Regular, tested, encrypted
7. **Monitoring** - Know when things go wrong
8. **Updates** - Keep dependencies current

### Common Security Mistakes to Avoid

❌ Using default secrets
❌ Disabling security features "temporarily"
❌ Logging sensitive data (passwords, tokens)
❌ Not testing backups
❌ Skipping updates
❌ No monitoring
❌ Weak passwords
❌ Trusting user input

---

**Last Updated**: January 2024
**Next Review**: April 2024
**Status**: Pre-Production Setup Required

For questions: security@mindful-gaming-zone.com
