# FiNimbus Glossary — Implementation Spec

**Repo:** `/Users/collinwilkins/Projects/finimbus-market-feedback`
**Stack:** Vite + React 19 + React Router 7 + Tailwind CSS 4
**Architecture:** Fully programmatic — all pages generated from JS data files. No new routes or page components needed for new pages.

---

## Current Inventory

### Financial Terms (7)

| Slug | Name | Category | Priority |
|------|------|----------|----------|
| `gross-profit-margin` | Gross Profit Margin | profitability | 1 |
| `current-ratio` | Current Ratio | liquidity | 2 |
| `days-sales-outstanding` | Days Sales Outstanding (DSO) | cashFlow | 3 |
| `net-profit-margin` | Net Profit Margin | profitability | 4 |
| `burn-rate` | Burn Rate | cashFlow | 5 |
| `cash-flow` | Cash Flow | cashFlow | 6 |
| `overhead-costs` | Overhead Costs | operations | 7 |

### Categories

```js
FINANCIAL_CATEGORIES = {
  profitability: 'Profitability',
  liquidity: 'Liquidity',
  cashFlow: 'Cash Flow',
  operations: 'Operations',
}
```

### Industries (9)

| Slug | Name | Priority |
|------|------|----------|
| `cleaning-companies` | Cleaning Companies | 1 |
| `salons` | Salons & Spas | 2 |
| `restaurants` | Restaurants | 3 |
| `hvac-contractors` | HVAC Contractors | 4 |
| `marketing-agencies` | Marketing Agencies | 5 |
| `consulting-firms` | Consulting Firms | 6 |
| `freelancers` | Freelancers | 7 |
| `creative-agencies` | Creative Agencies | 8 |
| `it-services` | IT Services | 9 |

### Content Entries (31 complete pages)

All 5 original terms × 5 original industries = 25 pages, plus:
- `net-profit-margin` × consulting-firms, freelancers, creative-agencies, it-services = 4 pages
- `cash-flow` × marketing-agencies = 1 page
- `overhead-costs` × marketing-agencies = 1 page

**Total: 31 detail pages live**

---

## File Architecture

| File | Purpose |
|------|---------|
| `src/data/glossary/financialTerms.js` | Term definitions, formulas, categories, SEO metadata |
| `src/data/glossary/industries.js` | Industry profiles, challenges, typical costs, SEO metadata |
| `src/data/glossary/termIndustryContent.js` | Content entries (key: `term-slug\|industry-slug`). Each entry has: `whyItMatters`, `benchmarks`, `example`, `problems`, `improvements`, `ctaContext`, `metaDescription`. Optional: `faqEntries` |
| `src/data/glossary/relatedTerms.js` | Related terms graph for internal linking (`directlyRelated`, `crossCategory`, `nextLevel`, `relationship`) |
| `src/data/glossary/glossaryData.js` | Main export combining all data files. Provides helpers: `generateAllGlossaryPages()`, `getRelatedGlossaryPages()`, `getSameTermDifferentIndustryPages()`, `getTermPages()`, `getIndustryPages()`, `getGlossaryPage()`, `getGlossaryStats()`, `validateGlossaryData()` |

### Page Components

| File | Purpose |
|------|---------|
| `src/pages/glossary/GlossaryTermIndustryPage.jsx` | Detail page (`/glossary/:termSlug/:industrySlug`). Renders all sections + FAQ schema + breadcrumb schema |
| `src/pages/glossary/GlossaryTermPage.jsx` | Term hub (`/glossary/:termSlug`) — lists all industries for a term |
| `src/pages/glossary/GlossaryIndustryPage.jsx` | Industry hub (`/glossary/industry/:industrySlug`) — lists all terms for an industry |
| `src/pages/glossary/GlossaryIndexPage.jsx` | Glossary index (`/glossary`) — lists all terms and industries |

### Section Components

| Component | Purpose |
|-----------|---------|
| `GlossaryHeader` | Page header with breadcrumbs |
| `MetricBenchmarkCard` | Healthy/warning/danger benchmark display |
| `ExampleCalculation` | Worked example with formula and breakdown table |
| `MarginCalculator` | Interactive calculator (profitability terms only) |
| `ProblemsSection` / `ImprovementsSection` | Accordion-style problem/solution cards |
| `KeyTakeaways` | Auto-generated key takeaways |
| `RelatedTermsGrid` | Same-industry, different-term internal links |
| `RelatedIndustriesGrid` | Same-term, different-industry internal links |
| `GlossaryCTA` | Inline and footer CTAs |

### Scripts

| Script | Purpose |
|--------|---------|
| `scripts/generate-sitemap.js` | Generates `public/sitemap.xml` from all glossary data + blog posts. Runs automatically as prebuild step. |
| `scripts/generate-og-image.js` | Generates OG images using Puppeteer. Usage: `node scripts/generate-og-image.js --headline "Title" --subtext "Subtitle" --output "public/glossary/og-slug.png"` |

---

## URL Structure

```
/glossary                                    — Index page
/glossary/:termSlug                          — Term hub (e.g., /glossary/net-profit-margin)
/glossary/industry/:industrySlug             — Industry hub (e.g., /glossary/industry/marketing-agencies)
/glossary/:termSlug/:industrySlug            — Detail page (e.g., /glossary/net-profit-margin/marketing-agencies)
```

---

## How to Add a New Page

### Adding a new term×industry detail page

1. Add content entry to `src/data/glossary/termIndustryContent.js` with key `term-slug|industry-slug`
2. If new term: add to `src/data/glossary/financialTerms.js` + `relatedTerms.js`
3. If new industry: add to `src/data/glossary/industries.js`
4. Update `relatedTerms.js` cross-references
5. Run `npm run build` (sitemap regenerates automatically via prebuild)
6. Run `node scripts/generate-og-image.js` for the new page

### Content entry shape

```js
'term-slug|industry-slug': {
  whyItMatters: 'String — industry-specific explanation...',

  benchmarks: {
    healthy: { min: 15, max: 25, label: '15-25%' },
    warning: { min: 8, max: 14, label: '8-14%' },
    danger: { max: 7, label: 'Below 8%' },
    context: 'Additional benchmark context...',
    source: 'Source attribution...',
  },

  example: {
    businessName: 'Example Business Name',
    revenue: 480000,
    cogs: null,
    calculation: '($67,200 / $480,000) × 100 = 14%',
    breakdown: [
      { label: 'Line Item', value: 480000, description: 'Description' },
      // ... more line items
    ],
    interpretation: 'What this result means...',
  },

  problems: [
    {
      problem: 'Problem title',
      symptom: 'How you notice it',
      impact: 'Why it matters with numbers',
    },
    // ... 3-4 problems
  ],

  improvements: [
    {
      action: 'Action title',
      howTo: 'Step-by-step instructions',
      expectedImpact: 'Measurable expected result',
    },
    // ... 3-4 improvements
  ],

  ctaContext: {
    inline: 'Mid-page CTA copy',
    footer: 'Footer CTA copy',
  },

  metaDescription: 'SEO meta description (155 chars)',

  // Optional — only on pages with custom FAQ schema
  faqEntries: [
    { question: 'Question text?', answer: 'Answer text.' },
    // ... 5-6 entries
  ],
}
```

### Industry entry shape

```js
{
  slug: 'industry-slug',
  name: 'Display Name',
  namePlural: 'Display Name Plural',
  nameVariations: ['variation1', 'variation2'],
  description: 'Industry description',
  businessType: 'Service',
  commonChallenges: ['Challenge 1', 'Challenge 2', ...],
  typicalCosts: {
    labor: '50-65% of revenue',
    software: '5-10% of revenue',
    // ... industry-specific cost categories
  },
  keywords: 'SEO keywords comma separated',
  industryKeywords: 'More specific industry keywords',
  avgRevenue: '$200K-$2M',
  employeeCount: '3-20 employees',
  priority: 5,
}
```

### Financial term entry shape

```js
{
  slug: 'term-slug',
  name: 'Display Name',
  category: 'profitability',     // profitability | liquidity | cashFlow | operations
  categoryName: 'Profitability',
  shortDefinition: 'One-line plain English definition',
  longDefinition: 'Extended definition with context...',
  formulaMathematical: '(Revenue - Expenses) / Revenue × 100',
  formulaPlainEnglish: 'Plain English formula explanation',
  whyItMatters: 'Why this metric matters (for hub page)',
  relatedTerms: ['other-term-slug', ...],
  keywords: 'SEO keywords',
  difficulty: 'beginner',        // beginner | intermediate | advanced
  priority: 4,
}
```

---

## On-Page Features

### MarginCalculator (profitability terms only)
- Renders on `gross-profit-margin` and `net-profit-margin` pages
- Two inputs: Total Revenue, Total Expenses
- Shows result with color-coded indicator based on page benchmarks
- CTA to Financial Health Score

### Enhanced FAQ Schema
- If `content.faqEntries` exists, uses those for JSON-LD FAQPage schema instead of generic auto-generated FAQs
- Also renders a visible FAQ accordion section on the page
- Currently only `net-profit-margin|marketing-agencies` has custom `faqEntries`

### Cross-Vertical Grid (RelatedIndustriesGrid)
- Shows same-term pages for other industries
- Uses `getSameTermDifferentIndustryPages()` helper
- Renders after RelatedTermsGrid section

---

## Content Standards

- All benchmarks use hedge language ("typically," "tend to," "often")
- No fabricated statistics, quotes, or data points (see CLAUDE.md accuracy rules)
- Examples use realistic but fictional business names
- Problems include specific symptoms and quantified impact
- Improvements include actionable how-to steps and expected measurable impact
- Voice is plain English, direct, Olivia-friendly (target persona)
