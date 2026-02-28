# Fraud Prevention & Refund Management System

## 🎯 Overview

A comprehensive fraud prevention system for AI Content Studio that automatically detects and prevents credit refund abuse. The system tracks all refunds, revokes credits, handles negative balances, flags suspicious users, and provides a full admin dashboard for monitoring.

---

## ✅ Implemented Features

### 1. **Database Schema Updates** (Prisma)

#### User Model Enhancements
Added fraud prevention fields to track refund history:
- `isFlagged` - Boolean flag for suspicious users
- `flaggedReason` - Detailed reason for flagging
- `flaggedAt` - Timestamp when user was flagged
- `totalRefunds` - Counter for lifetime refunds
- `totalRefundedAmount` - Total dollar amount refunded

#### New RefundEvent Model
Comprehensive tracking of all refund events:
- Stripe refund, charge, and payment intent IDs
- Amount refunded (USD) and credits revoked
- User's balance at time of refund
- Negative balance detection and tracking
- Automated flagging status
- Admin notes and status tracking

#### CreditTransaction Enum Update
Added `REFUND` type to track credit revocations

---

### 2. **Stripe Webhook Enhancements**

Location: `src/app/api/payments/webhook/route.ts`

#### New Event Handlers

**`charge.refunded` Event** ✅
- Automatically detects when a Stripe refund occurs
- Finds the associated purchase and user
- Revokes the exact number of credits purchased
- Handles negative balances (user spent credits before refunding)
- Creates detailed audit trail
- Flags users for suspicious behavior

**`charge.dispute.created` Event** ✅
- Logs dispute creation for manual review
- High-priority fraud alert
- Admin notification system ready for extension

#### Fraud Detection Logic

**Automatic Flagging Triggers:**
1. **Negative Balance**: User spent credits before requesting refund
   - System calculates how many credits user "owes"
   - Flags immediately with detailed reason

2. **Multiple Refunds**: User has 2+ refunds
   - Pattern suggests potential abuse
   - Requires admin review

**What Happens on Refund:**
```
1. Find purchase by Stripe payment intent ID
2. Get user's current credit balance
3. Calculate if refund creates negative balance
4. Revoke credits (balance can go negative)
5. Update purchase status to REFUNDED
6. Create credit transaction record (REFUND type)
7. Create RefundEvent for audit trail
8. Flag user if suspicious patterns detected
9. Log everything for admin review
```

---

### 3. **Admin Dashboard**

Location: `src/app/admin/page.tsx`

#### Features

**User Management Tab:**
- View all users with credit and refund statistics
- Filter by flagged users only
- Search by email or name
- See credit balance, total used, refund count
- Flag/unflag users manually
- Adjust credits with reason tracking
- Color-coded status (red for flagged users)

**Refund Monitoring Tab:**
- View all refund events
- Filter by flagged refunds only
- See refund amount, credits revoked, user balance changes
- Identify negative balance situations
- Review fraud patterns
- Color-coded alerts for suspicious refunds

#### Admin API Routes

**GET `/api/admin/users`**
- Fetch users with pagination
- Filter by flagged status
- Search functionality
- Returns user stats and refund counts

**PATCH `/api/admin/users`**
Actions supported:
- `flag` - Manually flag a user
- `unflag` - Remove flag from user
- `adjustCredits` - Add/remove credits with audit trail
- `changeRole` - Make user admin/subscriber

**GET `/api/admin/refunds`**
- Fetch refund events with pagination
- Filter by flagged status
- Aggregate statistics
- User information included

**PATCH `/api/admin/refunds`**
- Update refund status
- Add admin notes
- Mark as reviewed/resolved

---

## 🔐 Security Features

### 1. **Automatic Fraud Detection**
- Real-time refund monitoring via Stripe webhooks
- Negative balance tracking
- Pattern-based user flagging
- Detailed audit logs

### 2. **Credit Revocation**
- Immediate credit removal on refund
- Negative balances allowed (user debt tracking)
- Atomic database transactions prevent race conditions
- Full credit transaction history

### 3. **Admin Oversight**
- Comprehensive dashboard for monitoring
- Manual review capabilities
- User flagging/unflagging
- Credit adjustments with reasons

### 4. **Audit Trail**
- Every refund creates a RefundEvent record
- All credit changes logged in CreditTransaction
- Timestamps and metadata preserved
- Admin actions trackable

---

## 📋 Testing Checklist

Before going live, test these scenarios:

### Test 1: Normal Refund (No Fraud)
- [ ] User purchases credits
- [ ] User requests refund before spending any credits
- [ ] Credits are revoked correctly
- [ ] User balance goes back to 0
- [ ] RefundEvent created with `userFlagged: false`
- [ ] Purchase status updated to REFUNDED

### Test 2: Negative Balance (Fraud Alert)
- [ ] User purchases 100 credits
- [ ] User spends 50 credits
- [ ] User requests refund
- [ ] System revokes 100 credits (balance becomes -50)
- [ ] User flagged automatically
- [ ] RefundEvent shows `hadNegativeBalance: true`
- [ ] Admin can see debt in dashboard

### Test 3: Multiple Refunds (Pattern Detection)
- [ ] User purchases credits
- [ ] User refunds (1st time - no flag)
- [ ] User purchases again
- [ ] User refunds (2nd time - flagged)
- [ ] System flags user for multiple refunds
- [ ] Admin dashboard shows both refund events

### Test 4: Admin Dashboard
- [ ] Admin can log in to `/admin`
- [ ] Non-admin users blocked from admin routes
- [ ] User list displays correctly
- [ ] Refund list displays correctly
- [ ] Search and filters work
- [ ] Flag/unflag actions work
- [ ] Credit adjustments work

---

## 🚀 Deployment Steps

### 1. Database Migration
```bash
cd "AI Content Studio/Website"
npx prisma generate
npx prisma db push
```

This will add the new fields and RefundEvent table to your database.

### 2. Create First Admin User

Run this SQL in your PostgreSQL database:
```sql
UPDATE users
SET role = 'admin'
WHERE email = 'your-email@example.com';
```

Or use Prisma Studio:
```bash
npx prisma studio
```
Then manually update a user's role to 'admin'.

### 3. Configure Stripe Webhooks

Add these webhook events in your Stripe Dashboard:
- `charge.refunded` ✅ **CRITICAL**
- `charge.dispute.created` (optional but recommended)

Webhook URL: `https://yourdomain.com/api/payments/webhook`

### 4. Test Webhook Locally

Use Stripe CLI for local testing:
```bash
stripe listen --forward-to localhost:3000/api/payments/webhook

# Trigger test refund
stripe refunds create --charge=ch_xxxxx
```

### 5. Deploy to Production
```bash
git add .
git commit -m "feat: Add fraud prevention and refund management system"
git push
```

Vercel will automatically deploy the changes.

---

## 📊 Monitoring & Alerts

### Key Metrics to Watch

1. **Flagged Users Count**
   - Check admin dashboard daily
   - Investigate flagged users promptly

2. **Negative Balance Users**
   - Review users with negative credits
   - Decide on collection policy

3. **Refund Rate**
   - Normal: < 5% of purchases
   - Warning: 5-10%
   - Critical: > 10%

4. **Repeat Refunders**
   - Users with 2+ refunds need review
   - Consider blocking habitual abusers

### Admin Workflow

1. **Daily**: Check for newly flagged users
2. **Weekly**: Review all refund events
3. **Monthly**: Analyze refund patterns and trends
4. **As Needed**: Adjust user credits, unflag false positives

---

## 🔧 Configuration

### Fraud Detection Thresholds

You can adjust these in `webhook/route.ts`:

```typescript
// Flag user if they have 2 or more refunds
const shouldFlag = hadNegativeBalance || updatedUser.totalRefunds >= 2;
```

**Recommended Settings:**
- **Starter/Small Sites**: 2 refunds = flag
- **Medium Sites**: 3 refunds = flag
- **Enterprise**: 5 refunds = flag

### Negative Balance Policy

**Current Behavior**: System allows negative balances to track debt.

**Options:**
1. **Track Only** (current) - Let balance go negative, admin decides
2. **Block Usage** - Prevent users with negative balance from generating workflows
3. **Auto-Suspend** - Automatically suspend flagged users

To implement blocking, add this to `credits/use/route.ts`:
```typescript
if (user.credits < 0) {
  return NextResponse.json(
    { error: 'Account has negative balance. Please contact support.' },
    { status: 403 }
  );
}
```

---

## 🛡️ Best Practices

### For Admins

1. **Review Flags Promptly**
   - Don't let flagged users pile up
   - Investigate within 24-48 hours

2. **Document Decisions**
   - Use admin notes in refund events
   - Keep records of why users were flagged/unflagged

3. **Fair Treatment**
   - Not all refunds are fraud
   - Consider legitimate reasons (product issues, etc.)

4. **Clear Communication**
   - Inform users why they're flagged
   - Provide path to resolution

### For Developers

1. **Monitor Logs**
   - Watch for `🚨 USER FLAGGED` logs
   - Check Stripe webhook delivery

2. **Test Thoroughly**
   - Use Stripe test mode
   - Simulate all fraud scenarios

3. **Keep Updated**
   - Monitor Stripe API changes
   - Update webhook handlers as needed

---

## 📁 Files Modified/Created

### Schema Changes
- `prisma/schema.prisma` - Added fraud fields to User, created RefundEvent model

### API Routes Created
- `src/app/api/admin/users/route.ts` - User management API
- `src/app/api/admin/refunds/route.ts` - Refund monitoring API

### API Routes Modified
- `src/app/api/payments/webhook/route.ts` - Added refund handling logic

### Pages Created
- `src/app/admin/page.tsx` - Admin dashboard UI

---

## ❓ FAQ

**Q: What happens if a user has negative credits?**
A: They can still use the site unless you implement blocking. Negative balance is tracked for admin review.

**Q: Can I manually refund without flagging?**
A: Yes, use the admin dashboard to adjust credits with a reason. This won't trigger fraud alerts.

**Q: How do I unflag a false positive?**
A: Use the admin dashboard "Unflag" button. The flag reason is cleared but refund history remains.

**Q: What if a user disputes a flag?**
A: Review their RefundEvent records, check the audit trail, and unflag if legitimate.

**Q: Can I customize flagging rules?**
A: Yes! Edit the `handleChargeRefunded` function in `webhook/route.ts`.

---

## 🎉 Success!

Your fraud prevention system is now ready to protect your business from credit refund abuse. The system will:

✅ Automatically detect and prevent fraud
✅ Track all refunds comprehensively
✅ Flag suspicious users for review
✅ Provide admin tools for management
✅ Create detailed audit trails
✅ Handle edge cases like negative balances

**Next Steps:**
1. Deploy to production
2. Configure Stripe webhooks
3. Create your first admin user
4. Test with a small refund
5. Monitor the admin dashboard

Need help? Check the testing checklist above or review the code comments in each file.

---

**Built for AI Content Studio Pro** 🚀
**Date:** December 2025
**Version:** 1.0.0
