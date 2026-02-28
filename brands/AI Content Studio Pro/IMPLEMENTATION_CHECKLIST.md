# Sanity Studio Customization - Implementation Checklist

## Quick Start Guide

This checklist will help you implement the Sanity Studio customizations in the correct order for maximum impact with minimum effort.

---

## Phase 1: Foundation (Day 1) - IMMEDIATE IMPACT

**Time Estimate:** 3-4 hours
**Priority:** CRITICAL
**Impact:** HIGH

### Step 1: Backup Current Files (5 minutes)

```bash
# Navigate to your project
cd "C:\Users\Oliver Productions\Desktop\1.SMG-BUSINESS\Side-Brands\AI Content Studio\Website"

# Create backup directory
mkdir -p backups/sanity-original

# Backup current structure
cp src/sanity/structure.ts backups/sanity-original/
cp src/sanity/schemaTypes/postType.ts backups/sanity-original/
```

- [ ] Backup `structure.ts`
- [ ] Backup `postType.ts`
- [ ] Backup `tutorialType.ts`
- [ ] Backup `newsletterType.ts`

### Step 2: Implement Enhanced Structure (1 hour)

**File:** `src/sanity/structure.ts`

- [ ] Open `src/sanity/structure.enhanced.ts`
- [ ] Copy all content
- [ ] Replace content in `src/sanity/structure.ts`
- [ ] Save file

**Test:**
```bash
npm run dev
```

- [ ] Studio loads without errors
- [ ] New "AI Content Studio" title visible
- [ ] Dashboard section appears
- [ ] "Recently Edited" works
- [ ] "All Drafts" shows only drafts
- [ ] Content type filters work (Blog Posts, Workflow Guides, etc.)

### Step 3: Enhanced Post Type Preview (30 minutes)

**File:** `src/sanity/schemaTypes/postType.ts`

Option A - Full replacement:
- [ ] Copy `postType.enhanced.ts` to `postType.ts`
- [ ] Test in Studio

Option B - Just update preview (safer):
- [ ] Open current `postType.ts`
- [ ] Find the `preview:` section (around line 98)
- [ ] Replace with enhanced preview from `postType.enhanced.ts` (lines 226-282)
- [ ] Save and test

**Test:**
- [ ] Post previews show status emoji (✏️/⏰/✅)
- [ ] Content type emoji shows (📝/📋/🎓)
- [ ] Featured posts show ⭐
- [ ] Author name displays correctly

### Step 4: Add Workflow State to Post Type (1 hour)

**File:** `src/sanity/schemaTypes/postType.ts`

Add these fields to your existing postType (after publishedAt field):

```typescript
defineField({
  name: 'workflowState',
  title: 'Review Status',
  type: 'string',
  options: {
    list: [
      {title: '✏️ Draft - Work in Progress', value: 'draft'},
      {title: '👀 Ready for Review', value: 'review'},
      {title: '✅ Approved for Publishing', value: 'approved'},
      {title: '🔄 Needs Revision', value: 'revision'},
    ],
    layout: 'radio',
  },
  initialValue: 'draft',
  validation: (Rule) => Rule.required(),
}),

defineField({
  name: 'reviewNotes',
  title: 'Review Notes',
  type: 'text',
  rows: 4,
  description: '📝 Internal notes about what needs to be changed',
  hidden: ({document}) => !['review', 'revision'].includes(document?.workflowState),
}),
```

- [ ] Add `workflowState` field
- [ ] Add `reviewNotes` field
- [ ] Update preview to show workflow status
- [ ] Test field visibility (reviewNotes only shows when needed)

### Step 5: Test Everything (30 minutes)

- [ ] Create a new blog post
- [ ] All fields visible
- [ ] Validation messages helpful
- [ ] Preview looks good
- [ ] Can filter by content type
- [ ] Dashboard shows recent edits
- [ ] Workflow state updates preview

---

## Phase 2: Enhanced Validation (Day 2)

**Time Estimate:** 2 hours
**Priority:** HIGH
**Impact:** HIGH

### Step 1: Create Validation Utilities

Create new file: `src/sanity/validation/index.ts`

```typescript
import {Rule, ValidationContext} from 'sanity'

export const validateSlug = (Rule: Rule) =>
  Rule.required().custom((slug, context: ValidationContext) => {
    if (!slug?.current) {
      return 'Slug is required for publishing'
    }

    const slugPattern = /^[a-z0-9]+(?:-[a-z0-9]+)*$/
    if (!slugPattern.test(slug.current)) {
      return 'Slug can only contain lowercase letters, numbers, and hyphens'
    }

    if (slug.current.length > 96) {
      return 'Slug is too long (max 96 characters)'
    }

    return true
  })

export const validateExcerpt = (Rule: Rule) =>
  Rule.max(160)
    .error('Search engines show only ~160 characters')
    .min(50)
    .warning('Excerpt should be at least 50 characters')

export const validateFeaturedImage = (Rule: Rule) =>
  Rule.custom((image, context: ValidationContext) => {
    if (!image) {
      return 'Featured image is recommended for better engagement'
    }

    if (!image.alt || image.alt.trim() === '') {
      return 'Please add alt text for accessibility'
    }

    return true
  }).warning()
```

- [ ] Create `validation` directory
- [ ] Create `index.ts` with validation functions
- [ ] Export all validators

### Step 2: Apply to Schemas

Update `postType.ts`:

```typescript
import {validateSlug, validateExcerpt, validateFeaturedImage} from '../validation'

// Then in fields:
defineField({
  name: 'slug',
  type: 'slug',
  validation: validateSlug,
}),

defineField({
  name: 'excerpt',
  type: 'text',
  validation: validateExcerpt,
}),

defineField({
  name: 'mainImage',
  type: 'image',
  validation: validateFeaturedImage,
}),
```

- [ ] Import validators
- [ ] Apply to slug field
- [ ] Apply to excerpt field
- [ ] Apply to mainImage field
- [ ] Test validation messages

### Step 3: Enhance Field Descriptions

Go through each schema and add helpful descriptions:

- [ ] Add emoji to field titles
- [ ] Add usage examples
- [ ] Add SEO tips
- [ ] Add character count guidance

Example:
```typescript
defineField({
  name: 'title',
  title: 'Title',
  description: '📝 Compelling headline (50-60 characters works best for SEO)',
  // ...
}),
```

---

## Phase 3: Custom Components (Week 2)

**Time Estimate:** 4-6 hours
**Priority:** MEDIUM
**Impact:** VERY HIGH

### Prerequisites

```bash
npm install react-markdown date-fns
npm install -D @types/react-markdown
```

- [ ] Install dependencies
- [ ] Create `components` directory

### Component 1: Prompt List Editor (Optional)

Only if you're managing prompt libraries:

- [ ] Create `src/sanity/components/PromptListEditor.tsx`
- [ ] Add to relevant schema
- [ ] Test functionality

### Component 2: Better Descriptions Component

Create reusable help text component:

```typescript
// src/sanity/components/FieldHelp.tsx
import {Card, Text} from '@sanity/ui'

export const FieldHelp = ({text}: {text: string}) => (
  <Card padding={2} radius={2} tone="primary" marginTop={2}>
    <Text size={1}>{text}</Text>
  </Card>
)
```

- [ ] Create FieldHelp component
- [ ] Use in schema descriptions

---

## Phase 4: Visual Polish (Week 2-3)

**Time Estimate:** 2-3 hours
**Priority:** LOW-MEDIUM
**Impact:** MEDIUM

### Custom Theme (Optional but Nice)

Create `src/sanity/theme.ts`:

```typescript
import {buildTheme} from '@sanity/ui/theme'

export const customTheme = buildTheme({
  base: 'light',
  brand: {
    primary: '#6366f1',
    accent: '#8b5cf6',
  },
})
```

Update `sanity.config.ts`:

```typescript
import {customTheme} from './src/sanity/theme'

export default defineConfig({
  // ...
  theme: customTheme,
  studio: {
    components: {
      logo: () => (
        <div style={{padding: '0.5em', fontSize: '1.5em', fontWeight: 'bold'}}>
          🤖 AI Content Studio
        </div>
      ),
    },
  },
})
```

- [ ] Create theme file
- [ ] Add to config
- [ ] Add custom logo
- [ ] Test appearance

---

## Phase 5: Advanced Features (Optional - Week 3-4)

**Time Estimate:** 6-8 hours
**Priority:** LOW
**Impact:** HIGH (for power users)

### Live Preview Plugin

**Only implement if you have API preview routes set up.**

- [ ] Create `src/sanity/plugins/livePreview.ts`
- [ ] Add to config
- [ ] Create preview API route in Next.js
- [ ] Test split-screen preview

### Custom Document Actions

Create quick actions:

- [ ] Duplicate action
- [ ] Preview in new tab action
- [ ] Bulk publish action (advanced)

---

## Testing Checklist

After each phase, verify:

### Functionality
- [ ] Studio loads without errors
- [ ] No TypeScript errors
- [ ] Can create new documents
- [ ] Can edit existing documents
- [ ] Can publish/unpublish
- [ ] Previews render correctly

### Navigation
- [ ] Dashboard shows correct items
- [ ] Filters work as expected
- [ ] Search still works
- [ ] Can navigate between sections

### Data Integrity
- [ ] Existing content not affected
- [ ] New fields have defaults
- [ ] Old documents still editable
- [ ] No broken references

### Performance
- [ ] Studio loads in <3 seconds
- [ ] Navigation is snappy
- [ ] Large lists paginate correctly
- [ ] Images load properly

---

## Rollback Plan

If something goes wrong:

```bash
# Restore from backup
cd "C:\Users\Oliver Productions\Desktop\1.SMG-BUSINESS\Side-Brands\AI Content Studio\Website"
cp backups/sanity-original/structure.ts src/sanity/
cp backups/sanity-original/postType.ts src/sanity/schemaTypes/

# Clear cache and restart
rm -rf .next
npm run dev
```

- [ ] Keep backups for at least 1 week
- [ ] Test rollback procedure before major changes
- [ ] Document any custom modifications

---

## Success Metrics

Track these before and after implementation:

**Before:**
- Time to find a draft: ___ seconds
- Time to create a post: ___ minutes
- Publishing errors per week: ___
- Team satisfaction: ___/10

**After (Week 1):**
- Time to find a draft: ___ seconds (target: 50% reduction)
- Time to create a post: ___ minutes (target: 30% reduction)
- Publishing errors per week: ___ (target: 70% reduction)
- Team satisfaction: ___/10 (target: +3 points)

---

## Support & Troubleshooting

### Common Issues

**Issue:** "Cannot find module" errors
**Solution:** Run `npm install` and restart dev server

**Issue:** Schema validation errors
**Solution:** Check that all field names match existing data

**Issue:** Preview not showing
**Solution:** Clear browser cache, check document has required fields

**Issue:** Custom components not rendering
**Solution:** Check import paths, verify component exports

### Getting Help

1. Check Sanity docs: https://www.sanity.io/docs
2. Search GitHub issues: https://github.com/sanity-io/sanity/issues
3. Join Slack: https://slack.sanity.io
4. Review error messages carefully (they're usually helpful)

---

## Next Steps After Implementation

1. **Gather Team Feedback** (Week 2)
   - Survey team members
   - Observe usage patterns
   - Identify pain points

2. **Iterate** (Week 3-4)
   - Adjust based on feedback
   - Add requested features
   - Refine workflows

3. **Document** (Ongoing)
   - Create team guidelines
   - Record common tasks
   - Build knowledge base

4. **Optimize** (Month 2)
   - Add advanced features
   - Integrate with other tools
   - Automate repetitive tasks

---

## Completion

Date Started: __________
Date Completed: __________

Phases Implemented:
- [ ] Phase 1: Foundation
- [ ] Phase 2: Enhanced Validation
- [ ] Phase 3: Custom Components
- [ ] Phase 4: Visual Polish
- [ ] Phase 5: Advanced Features

**Overall Assessment:**
- Impact on productivity: ___/10
- Team satisfaction: ___/10
- Worth the effort: Yes / No

**Lessons Learned:**
_____________________________________
_____________________________________
_____________________________________

**Future Improvements:**
_____________________________________
_____________________________________
_____________________________________
