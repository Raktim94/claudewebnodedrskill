---
name: seo
description: Technical SEO, content SEO, AI-search (GEO/AEO) optimization for websites, plus App Store Optimization (ASO) for mobile apps. Use this skill whenever the user mentions SEO, Google ranking, search traffic, meta tags, sitemaps, keywords, app store visibility, or asks how to get more visitors/downloads — and proactively whenever building any public-facing website or landing page so SEO is built in from the start.
---

# SEO & ASO — Rank in Search Engines, AI Answers, and App Stores

Modern discoverability (2026) has three fronts: classic Google/Bing ranking, AI answer engines (ChatGPT, Perplexity, Google AI Overviews — "GEO"), and app stores. Build for all three.

## Technical SEO (do this on every site you build)

**Every page gets:**

```html
<title>Primary Keyword — Brand | under 60 chars</title>
<meta name="description" content="Compelling 150–160 char summary with the keyword, written to earn the click.">
<link rel="canonical" href="https://example.com/page">
<meta property="og:title" content="..."><meta property="og:description" content="...">
<meta property="og:image" content="https://example.com/og.jpg"><!-- 1200x630 -->
<meta name="twitter:card" content="summary_large_image">
```

- One `<h1>` per page containing the primary keyword; logical h2/h3 hierarchy.
- Semantic HTML (`<main>`, `<article>`, `<nav>`) — crawlers and AI parsers reward structure.
- Clean URLs: `/blue-widgets` not `/p?id=123`; hyphens, lowercase, shallow paths.
- `sitemap.xml` (auto-generated on build) + `robots.txt` referencing it.
- Mobile-friendly and fast: Core Web Vitals are ranking signals (see performance-optimization skill).
- Descriptive `alt` text on all meaningful images; descriptive anchor text on links ("see pricing" not "click here").
- **Render content server-side.** Client-only rendering still hurts indexing and kills AI-crawler visibility. Use SSR/SSG (Next.js/Astro/Nuxt) for anything public.
- No duplicate content: canonical tags, consistent trailing slashes, redirect www/non-www.
- 301 redirects for moved pages; fix 404 chains; keep redirect chains ≤ 1 hop.

## Structured data (JSON-LD)

Add schema.org JSON-LD to every relevant page — it powers rich results AND helps AI engines cite you: `Organization` + `WebSite` on home, `Article` (with author/dates) on posts, `Product` (price, availability, reviews) on product pages, `FAQPage` where genuine FAQs exist, `BreadcrumbList` on deep pages, `SoftwareApplication` for app pages. Validate with Google's Rich Results Test.

## Content SEO

- One page = one search intent. Map keyword → intent (informational/commercial/transactional) → page type.
- Answer the query in the first 100 words, then go deep. Comprehensive beats long.
- Use the keyword in: title, h1, first paragraph, one h2, URL. Then write naturally — stuffing is a penalty.
- Internal linking: every page reachable ≤ 3 clicks from home; link related content with descriptive anchors; hub-and-spoke topic clusters.
- E-E-A-T: real author bylines, dates (show updated date), cite sources, about page, contact info.

## GEO/AEO — ranking in AI answers (the 2026 front)

- Write extractable answers: a direct 2–3 sentence answer under each question-style heading, then elaboration.
- Use FAQ sections, definition blocks, comparison tables, and stats with cited sources — AI engines quote these.
- Keep facts consistent across site + structured data; include the brand name near key claims (citation attribution).
- Allow AI crawlers in robots.txt (GPTBot, PerplexityBot, ClaudeBot, Google-Extended) unless the business says otherwise.
- Maintain an `llms.txt` summarizing the site for LLM crawlers.

## ASO — App Store Optimization (iOS + Android)

- **Title/Name** (30 chars): brand + strongest keyword ("Notely — AI Notes & PDF").
- iOS **subtitle** (30 chars) and **keyword field** (100 chars: comma-separated, no spaces, no duplicates of title words). Android: keyword-rich short description (80 chars) + long description (keywords naturally, ~2–3% density; Google indexes it, Apple doesn't index the description).
- Screenshots sell: first 2 screenshots = value proposition with caption text overlay; show real UI; localize top markets. Preview video for the first impression.
- Ratings: prompt happy users at success moments (in-app review API); respond to reviews (Android ranking signal); fix and reply — review keywords influence Android ranking.
- Iterate: track keyword rankings and conversion (impressions→installs), A/B test store listings (Product Page Optimization / Play experiments) monthly.

## Measurement

Set up Search Console + Bing Webmaster + analytics on day one. Track: indexed pages, impressions/CTR by query, Core Web Vitals, and (for apps) keyword ranks + conversion rate. SEO is iterative — report what to check after 4 weeks.

## Output format

For SEO audits, deliver a prioritized table: Priority | Issue | Impact | Fix. For new builds, confirm the checklist above is implemented and list the JSON-LD types added.
