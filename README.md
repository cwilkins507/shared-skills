# shared-skills

Monorepo for all Claude Code skills — custom-built and third-party. Every project symlinks here so edits propagate everywhere automatically.

## Skills (33)

### Custom

| Skill | Description |
|---|---|
| `ai-pattern-killer` | Three-agent pipeline that detects and removes AI-sounding patterns from content |
| `brand-voice` | FiNimbus brand voice (Finn persona) for marketing copy, product UI, and comms |
| `humanizer` | Transforms AI-generated text into authentic, human-sounding content |
| `reddit-patterns` | High-engagement Reddit response patterns, auto-generated from thread analysis |
| `voice-polish` | Applies writing patterns from exm7777, Dan Koe, and TheBeautyOfSaaS with AI-pattern removal |

### Third-Party

#### From [coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills)

| Skill | Description |
|---|---|
| `ab-test-setup` | Plan, design, and implement A/B tests |
| `analytics-tracking` | Set up and audit analytics tracking (GA4, GTM, event tracking) |
| `competitor-alternatives` | Create competitor comparison and alternative pages |
| `content-strategy` | Plan content strategy, topic clusters, and editorial direction |
| `copy-editing` | Edit, review, and improve existing marketing copy |
| `copywriting` | Write marketing copy for landing pages, homepages, and product pages |
| `email-sequence` | Create drip campaigns, nurture sequences, and lifecycle emails |
| `form-cro` | Optimize lead capture, contact, and demo request forms |
| `free-tool-strategy` | Plan and build free tools for lead gen and SEO |
| `launch-strategy` | Plan product launches, feature announcements, and go-to-market |
| `marketing-ideas` | 139 proven marketing approaches for SaaS and software |
| `marketing-psychology` | Apply psychological principles and mental models to marketing |
| `onboarding-cro` | Optimize post-signup onboarding and user activation |
| `page-cro` | Optimize conversions on landing pages, pricing pages, and homepages |
| `paid-ads` | Plan and run paid campaigns on Google, Meta, LinkedIn, and X |
| `paywall-upgrade-cro` | Create and optimize in-app paywalls and upgrade screens |
| `popup-cro` | Create and optimize popups, modals, and overlays for conversions |
| `pricing-strategy` | Research and design pricing tiers, packaging, and monetization |
| `product-marketing-context` | Create product marketing context docs for consistent messaging |
| `programmatic-seo` | Build SEO-driven template pages at scale |
| `referral-program` | Design referral programs, affiliate programs, and viral loops |
| `schema-markup` | Add and optimize JSON-LD structured data and rich snippets |
| `seo-audit` | Audit technical SEO, on-page SEO, and content optimization |
| `signup-flow-cro` | Optimize signup, registration, and trial activation flows |
| `social-content` | Create and optimize social media content across platforms |

#### From [onewave-ai/claude-skills](https://github.com/onewave-ai/claude-skills)

| Skill | Description |
|---|---|
| `cold-email-sequence-generator` | Generate personalized cold email sequences with A/B test subject lines |

#### From [vercel-labs/skills](https://github.com/vercel-labs/skills)

| Skill | Description |
|---|---|
| `find-skills` | Discover and install new Claude Code skills |

#### From [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills)

| Skill | Description |
|---|---|
| `excalidraw-diagram` | Generate Excalidraw diagrams for Obsidian and standard formats |

## Setup

All skills are symlinked from `~/.claude/skills/` to this repo:

```bash
# Global (available in every project)
ln -s ~/Projects/shared-skills/<skill-name> ~/.claude/skills/<skill-name>

# Project-local (available in a specific project)
ln -s ~/Projects/shared-skills/<skill-name> /path/to/project/.claude/skills/<skill-name>
```

## Structure

Each skill follows the same convention:

```
skill-name/
  SKILL.md              # Main skill definition (required)
  references/           # Supporting docs (optional)
  patterns/             # Pattern databases (optional, e.g. ai-pattern-killer)
  prompts/              # Agent prompts (optional, e.g. ai-pattern-killer)
```
