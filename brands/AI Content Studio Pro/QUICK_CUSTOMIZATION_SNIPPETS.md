# Sanity Studio Quick Customization Snippets

Essential code snippets you can copy-paste to customize your Sanity Studio quickly.

---

## Table of Contents
1. [Status Badges](#status-badges)
2. [Field Descriptions](#field-descriptions)
3. [Validation Rules](#validation-rules)
4. [Conditional Fields](#conditional-fields)
5. [Custom Previews](#custom-previews)
6. [Structure Filters](#structure-filters)
7. [Field Groups](#field-groups)
8. [Custom Ordering](#custom-ordering)

---

## Status Badges

### Add Visual Status to Previews

```typescript
preview: {
  select: {
    title: 'title',
    publishedAt: 'publishedAt',
    featured: 'featured',
    workflowState: 'workflowState',
  },
  prepare(selection) {
    const {title, publishedAt, featured, workflowState} = selection

    // Status indicator
    const now = new Date()
    const pubDate = publishedAt ? new Date(publishedAt) : null
    let statusEmoji = '✏️' // draft

    if (pubDate) {
      statusEmoji = pubDate > now ? '⏰' : '✅'
    }

    // Build subtitle
    const badges = []
    if (workflowState === 'review') badges.push('👀 In Review')
    if (featured) badges.push('⭐ Featured')

    return {
      title: `${statusEmoji} ${title}`,
      subtitle: badges.join(' • '),
      media: selection.media,
    }
  },
}
```

### Content Type Badges

```typescript
prepare(selection) {
  const {contentType} = selection

  const typeIcons = {
    blog: '📝',
    workflow: '📋',
    tutorial: '🎓',
  }

  return {
    title: `${typeIcons[contentType] || '📄'} ${selection.title}`,
    subtitle: selection.subtitle,
  }
}
```

---

## Field Descriptions

### SEO-Focused Descriptions

```typescript
defineField({
  name: 'title',
  title: 'Title',
  type: 'string',
  description: '📝 Compelling headline (50-60 characters for best SEO)',
}),

defineField({
  name: 'excerpt',
  title: 'Excerpt',
  type: 'text',
  description: '📄 Brief summary (appears in search results and social shares)',
}),

defineField({
  name: 'mainImage',
  title: 'Featured Image',
  type: 'image',
  description: '🖼️ Recommended: 1200x630px (16:9). Shows in social shares.',
}),

defineField({
  name: 'seoKeywords',
  title: 'Keywords',
  type: 'array',
  of: [{type: 'string'}],
  description: '🎯 3-5 keywords to target. Example: "AI tools", "automation"',
}),
```

### Helpful Placeholders

```typescript
defineField({
  name: 'readTime',
  title: 'Read Time',
  type: 'string',
  placeholder: '5 min read',
  description: '⏱️ Estimated reading time',
}),

defineField({
  name: 'videoUrl',
  title: 'Video URL',
  type: 'url',
  placeholder: 'https://youtube.com/watch?v=...',
  description: '🎥 YouTube or Vimeo link',
}),
```

---

## Validation Rules

### Required with Helpful Messages

```typescript
defineField({
  name: 'title',
  type: 'string',
  validation: (Rule) => Rule.required(),
}),
```

### Length Limits with Warnings

```typescript
// Soft limit (warning)
defineField({
  name: 'excerpt',
  type: 'text',
  validation: (Rule) =>
    Rule.max(160)
      .warning('Search engines show only ~160 characters')
      .min(50)
      .warning('Should be at least 50 characters'),
}),

// Hard limit (error)
defineField({
  name: 'slug',
  type: 'slug',
  validation: (Rule) =>
    Rule.required()
      .error('Slug is required for publishing'),
}),
```

### Custom Validation Functions

```typescript
// Slug format validation
defineField({
  name: 'slug',
  type: 'slug',
  validation: (Rule) =>
    Rule.custom((slug) => {
      if (!slug?.current) return 'Slug is required'

      const pattern = /^[a-z0-9]+(?:-[a-z0-9]+)*$/
      if (!pattern.test(slug.current)) {
        return 'Use only lowercase letters, numbers, and hyphens'
      }

      return true
    }),
}),

// Date validation
defineField({
  name: 'publishedAt',
  type: 'datetime',
  validation: (Rule) =>
    Rule.custom((date) => {
      if (!date) return true

      const sixMonthsFromNow = new Date()
      sixMonthsFromNow.setMonth(sixMonthsFromNow.getMonth() + 6)

      if (new Date(date) > sixMonthsFromNow) {
        return '⚠️ Scheduled more than 6 months ahead - is this correct?'
      }

      return true
    }),
}),

// Image alt text validation
defineField({
  name: 'mainImage',
  type: 'image',
  fields: [
    {
      name: 'alt',
      type: 'string',
      validation: (Rule) =>
        Rule.required()
          .min(10)
          .warning('Alt text should be descriptive'),
    },
  ],
}),
```

### Array Validation

```typescript
// Min/max items
defineField({
  name: 'categories',
  type: 'array',
  of: [{type: 'reference', to: {type: 'category'}}],
  validation: (Rule) =>
    Rule.min(1)
      .warning('Add at least one category')
      .max(3)
      .warning('Consider limiting to 3 categories'),
}),

// Keywords
defineField({
  name: 'seoKeywords',
  type: 'array',
  of: [{type: 'string'}],
  validation: (Rule) =>
    Rule.min(3)
      .max(10)
      .warning('3-5 keywords work best'),
}),
```

---

## Conditional Fields

### Show/Hide Based on Field Value

```typescript
// Master toggle field
defineField({
  name: 'hasAffiliate',
  title: 'Contains Affiliate Links',
  type: 'boolean',
  initialValue: false,
}),

// Conditional field
defineField({
  name: 'affiliateDisclaimer',
  title: 'Affiliate Disclaimer',
  type: 'text',
  hidden: ({document}) => !document?.hasAffiliate,
  validation: (Rule) =>
    Rule.custom((value, context) => {
      if (context.document?.hasAffiliate && !value) {
        return 'Disclaimer required when hasAffiliate is true'
      }
      return true
    }),
}),
```

### Multiple Conditions

```typescript
defineField({
  name: 'reviewNotes',
  title: 'Review Notes',
  type: 'text',
  hidden: ({document}) => {
    const state = document?.workflowState
    return !['review', 'revision'].includes(state)
  },
}),

defineField({
  name: 'stripeProductId',
  title: 'Stripe Product ID',
  type: 'string',
  hidden: ({document}) =>
    document?.salesPlatform !== 'stripe',
}),
```

### Read-Only Fields

```typescript
defineField({
  name: 'sentAt',
  title: 'Sent At',
  type: 'datetime',
  readOnly: true,
  description: 'Automatically set when newsletter is sent',
}),
```

---

## Custom Previews

### Basic Preview with Subtitle

```typescript
preview: {
  select: {
    title: 'title',
    author: 'author.name',
    media: 'mainImage',
  },
  prepare(selection) {
    return {
      title: selection.title,
      subtitle: selection.author ? `by ${selection.author}` : '',
      media: selection.media,
    }
  },
}
```

### Preview with Multiple Data Points

```typescript
preview: {
  select: {
    title: 'title',
    tool: 'tool.title',
    difficulty: 'difficulty',
    duration: 'duration',
    media: 'thumbnailImage',
  },
  prepare(selection) {
    const {title, tool, difficulty, duration} = selection

    const parts = [tool, difficulty, duration]
      .filter(Boolean)
      .join(' • ')

    return {
      title,
      subtitle: parts,
      media: selection.media,
    }
  },
}
```

### Preview with Computed Values

```typescript
preview: {
  select: {
    title: 'title',
    price: 'pricing.price',
    productType: 'productType',
    isActive: 'isActive',
    media: 'coverImage',
  },
  prepare(selection) {
    const {title, price, productType, isActive} = selection

    const priceDisplay = price ? `$${price}` : 'Free'
    const statusBadge = isActive ? '' : '🔴 Inactive'

    return {
      title: `${title} ${statusBadge}`,
      subtitle: `${productType} • ${priceDisplay}`,
      media: selection.media,
    }
  },
}
```

### Preview with Nested Data

```typescript
preview: {
  select: {
    title: 'title',
    issueNumber: 'issueNumber',
    status: 'status',
    scheduledFor: 'scheduledFor',
    sentAt: 'sentAt',
  },
  prepare(selection) {
    const {title, issueNumber, status, scheduledFor, sentAt} = selection

    // Format status
    let statusDisplay = status?.toUpperCase() || 'DRAFT'

    if (status === 'scheduled' && scheduledFor) {
      const date = new Date(scheduledFor)
      statusDisplay = `📅 ${date.toLocaleDateString()}`
    }

    if (status === 'sent' && sentAt) {
      const date = new Date(sentAt)
      statusDisplay = `✅ Sent ${date.toLocaleDateString()}`
    }

    return {
      title: `#${issueNumber || '?'} - ${title}`,
      subtitle: statusDisplay,
    }
  },
}
```

---

## Structure Filters

### Filter by Document Field

```typescript
S.listItem()
  .title('Blog Posts')
  .child(
    S.documentList()
      .title('Blog Posts')
      .filter('_type == "post" && contentType == "blog"')
      .params({})
  )
```

### Filter by Date Range

```typescript
// Drafts (no publishedAt)
S.documentList()
  .filter('_type == "post" && !defined(publishedAt)')
  .params({})

// Scheduled (future date)
S.documentList()
  .filter('defined(publishedAt) && publishedAt > now()')
  .params({})

// Published (past date)
S.documentList()
  .filter('defined(publishedAt) && publishedAt <= now()')
  .params({})
```

### Filter by Boolean

```typescript
// Featured items
S.documentList()
  .filter('featured == true')
  .params({})

// Active products
S.documentList()
  .filter('_type == "ebook" && isActive == true')
  .params({})
```

### Filter by Reference

```typescript
// Filter by category
S.documentList()
  .filter('_type == "tutorial" && references($categoryId)')
  .params({categoryId: 'specific-category-id'})

// Filter by author
S.documentList()
  .filter('author._ref == $authorId')
  .params({authorId: 'specific-author-id'})
```

### Multiple Filters

```typescript
S.documentList()
  .filter('_type == "post" && contentType == "blog" && featured == true')
  .params({})

S.documentList()
  .filter('_type in ["post", "tutorial"] && defined(publishedAt) && publishedAt > now()')
  .params({})
```

### Custom Ordering

```typescript
S.documentList()
  .filter('_type == "post"')
  .defaultOrdering([{field: 'publishedAt', direction: 'desc'}])

S.documentList()
  .filter('_type == "newsletter"')
  .defaultOrdering([
    {field: 'issueNumber', direction: 'desc'}
  ])
```

---

## Field Groups

### Basic Grouping

```typescript
defineType({
  name: 'post',
  type: 'document',
  groups: [
    {name: 'content', title: 'Content', default: true},
    {name: 'seo', title: 'SEO'},
    {name: 'settings', title: 'Settings'},
  ],
  fields: [
    defineField({
      name: 'title',
      type: 'string',
      group: 'content',
    }),
    defineField({
      name: 'seoKeywords',
      type: 'array',
      of: [{type: 'string'}],
      group: 'seo',
    }),
  ],
})
```

### Groups with Icons

```typescript
import {DocumentTextIcon, SearchIcon, CogIcon} from '@sanity/icons'

groups: [
  {
    name: 'content',
    title: 'Content',
    icon: DocumentTextIcon,
    default: true,
  },
  {
    name: 'seo',
    title: 'SEO & Metadata',
    icon: SearchIcon,
  },
  {
    name: 'settings',
    title: 'Settings',
    icon: CogIcon,
  },
]
```

---

## Custom Ordering

### Define Multiple Sort Options

```typescript
orderings: [
  {
    title: 'Published Date, Newest',
    name: 'publishedDesc',
    by: [{field: 'publishedAt', direction: 'desc'}],
  },
  {
    title: 'Published Date, Oldest',
    name: 'publishedAsc',
    by: [{field: 'publishedAt', direction: 'asc'}],
  },
  {
    title: 'Title A-Z',
    name: 'titleAsc',
    by: [{field: 'title', direction: 'asc'}],
  },
  {
    title: 'Recently Updated',
    name: 'updatedDesc',
    by: [{field: '_updatedAt', direction: 'desc'}],
  },
]
```

### Multiple Field Sorting

```typescript
orderings: [
  {
    title: 'Featured First, Then by Date',
    name: 'featuredFirst',
    by: [
      {field: 'featured', direction: 'desc'},
      {field: 'publishedAt', direction: 'desc'},
    ],
  },
]
```

---

## Initial Values

### Set Default Values

```typescript
defineField({
  name: 'featured',
  type: 'boolean',
  initialValue: false,
}),

defineField({
  name: 'contentType',
  type: 'string',
  options: {
    list: ['blog', 'workflow', 'tutorial'],
  },
  initialValue: 'blog',
}),

defineField({
  name: 'workflowState',
  type: 'string',
  initialValue: 'draft',
}),
```

### Dynamic Initial Values

```typescript
defineField({
  name: 'publishedAt',
  type: 'datetime',
  initialValue: () => new Date().toISOString(),
}),

defineField({
  name: 'author',
  type: 'reference',
  to: {type: 'author'},
  initialValue: () => ({
    _ref: 'default-author-id',
  }),
}),
```

---

## Quick Tips

### 1. Always Backup Before Making Changes
```bash
cp src/sanity/structure.ts src/sanity/structure.backup.ts
```

### 2. Test Changes Immediately
```bash
npm run dev
# Open http://localhost:3000/studio
```

### 3. Use TypeScript for Type Safety
Your schemas will autocomplete and catch errors.

### 4. Keep Descriptions User-Friendly
Think about your team members who aren't developers.

### 5. Use Emojis Sparingly
They're great for visual scanning but can be overwhelming.

### 6. Validation Should Guide, Not Block
Use `.warning()` for suggestions, `.error()` for requirements.

### 7. Test with Real Content
Create test documents to verify your customizations work.

### 8. Document Custom Behavior
Add comments explaining why you made certain choices.

---

## Common Patterns

### Pattern: Content Status Workflow

```typescript
// Add to any content type
defineField({
  name: 'workflowState',
  type: 'string',
  options: {
    list: [
      {title: '✏️ Draft', value: 'draft'},
      {title: '👀 Review', value: 'review'},
      {title: '✅ Approved', value: 'approved'},
    ],
    layout: 'radio',
  },
  initialValue: 'draft',
}),
```

### Pattern: Affiliate Link Management

```typescript
defineField({
  name: 'hasAffiliate',
  type: 'boolean',
  initialValue: false,
}),

defineField({
  name: 'affiliateLinks',
  type: 'array',
  of: [{
    type: 'object',
    fields: [
      {name: 'text', type: 'string', title: 'Link Text'},
      {name: 'url', type: 'url', title: 'Affiliate URL'},
      {name: 'commission', type: 'number', title: 'Commission %'},
    ],
  }],
  hidden: ({document}) => !document?.hasAffiliate,
}),
```

### Pattern: SEO Optimization

```typescript
defineField({
  name: 'seo',
  title: 'SEO',
  type: 'object',
  fields: [
    {
      name: 'metaTitle',
      type: 'string',
      title: 'Meta Title',
      validation: (Rule) => Rule.max(60),
    },
    {
      name: 'metaDescription',
      type: 'text',
      title: 'Meta Description',
      validation: (Rule) => Rule.max(160),
    },
    {
      name: 'keywords',
      type: 'array',
      of: [{type: 'string'}],
      validation: (Rule) => Rule.max(10),
    },
  ],
}),
```

---

## Copy-Paste Ready Examples

### Complete Enhanced Field

```typescript
defineField({
  name: 'excerpt',
  title: 'Excerpt',
  type: 'text',
  rows: 3,
  description: '📝 Compelling 1-2 sentence summary (shows in search and shares)',
  placeholder: 'Write a brief summary that makes people want to read more...',
  validation: (Rule) =>
    Rule.max(160)
      .error('Search engines show only ~160 characters')
      .min(50)
      .warning('Should be at least 50 characters for best results'),
}),
```

### Complete Preview

```typescript
preview: {
  select: {
    title: 'title',
    author: 'author.name',
    publishedAt: 'publishedAt',
    featured: 'featured',
    media: 'mainImage',
  },
  prepare(selection) {
    const {title, author, publishedAt, featured} = selection

    const now = new Date()
    const pubDate = publishedAt ? new Date(publishedAt) : null
    const status = !pubDate ? '✏️' : pubDate > now ? '⏰' : '✅'

    return {
      title: `${status} ${title}`,
      subtitle: [
        author && `by ${author}`,
        featured && '⭐ Featured'
      ].filter(Boolean).join(' • '),
      media: selection.media,
    }
  },
}
```

---

## Resources

- [Sanity Schema Types](https://www.sanity.io/docs/schema-types)
- [Structure Builder](https://www.sanity.io/docs/structure-builder-reference)
- [Validation](https://www.sanity.io/docs/validation)
- [Previews](https://www.sanity.io/docs/previews-list-views)
- [Icons](https://www.sanity.io/docs/icons-for-studio-v3)

---

**Pro Tip:** Start small! Pick 2-3 snippets that will have the biggest impact and implement those first. You can always add more later.
