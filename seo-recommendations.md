# index.html SEO/AEO Recommendations

Copy-pasteable snippets for `index.html`. I did **not** edit `index.html` (another agent owns it). Each item says exactly WHERE it goes and WHY. Ordered by impact within each section. Nothing here duplicates the existing Person / Organization / FAQPage / WebPage(Speakable) schema — it extends them.

Honesty note: no ratings, review counts, or `AggregateRating` are included — there is no verifiable review data, and fabricating it risks a Google structured-data manual action. Add `AggregateRating`/`Review` only if/when real, verifiable reviews exist.

---

## 1. Meta tags — add to `<head>`

### 1a. Insert immediately AFTER the `<link rel="canonical" ...>` line (line 13)
WHY: `theme-color` sets the mobile browser UI color; the geo tags reinforce Dubai/UAE relevance for local search; `robots` max-* directives let Google show full snippets and large image previews in AI/Search results.

```html
    <meta name="theme-color" content="#2B6777">
    <meta name="robots" content="index, follow, max-snippet:-1, max-image-preview:large, max-video-preview:-1">
    <meta name="geo.region" content="AE-DU">
    <meta name="geo.placename" content="Dubai">
    <meta name="geo.position" content="25.2048;55.2708">
    <meta name="ICBM" content="25.2048, 55.2708">
```
NOTE: this `robots` meta is a superset of the existing `<meta name="robots" content="index, follow">` on line 12 — **replace** line 12 with the richer one above (do not keep both).

### 1b. Insert into the Open Graph block, AFTER `og:url` (line 20)
WHY: `og:site_name` + `og:locale` improve link-preview rendering; explicit image width/height/alt/type make LinkedIn, WhatsApp, and Slack render the card reliably instead of dropping it.

```html
    <meta property="og:site_name" content="Ahmed Khaire">
    <meta property="og:locale" content="en_US">
    <meta property="og:image:secure_url" content="https://ahmedkhaire.com/assets/profile.jpg">
    <meta property="og:image:type" content="image/jpeg">
    <meta property="og:image:width" content="1200">
    <meta property="og:image:height" content="630">
    <meta property="og:image:alt" content="Ahmed Khaire, Founder & CEO of Nogbase, Dubai UAE">
```
VERIFY: set `og:image:width`/`height` to the real pixel dimensions of `assets/profile.jpg`. A 1200x630 (1.91:1) image is ideal for `summary_large_image`; if the current file is square/portrait, consider adding a dedicated 1200x630 share image.

### 1c. Insert into the Twitter Card block, AFTER `twitter:image` (line 30)
WHY: alt text for the Twitter/X card improves accessibility and preview quality.

```html
    <meta name="twitter:image:alt" content="Ahmed Khaire, Founder & CEO of Nogbase">
```

---

## 2. JSON-LD — new blocks

### 2a. WebSite schema — insert as a new `<script>` BEFORE the Person block (before line 33)
WHY: Declares the site entity and its publisher to search/AI engines; establishes the ahmedkhaire.com <-> Ahmed Khaire relationship independent of the Person node.

```html
    <!-- Schema.org: WebSite -->
    <script type="application/ld+json">
    {
        "@context": "https://schema.org",
        "@type": "WebSite",
        "name": "Ahmed Khaire",
        "url": "https://ahmedkhaire.com/",
        "inLanguage": "en",
        "author": { "@type": "Person", "name": "Ahmed Khaire" },
        "about": { "@type": "Organization", "name": "Nogbase", "url": "https://nogbase.ae" },
        "description": "Official site of Ahmed Khaire, Founder & CEO of Nogbase — a B2B PropTech SaaS platform for real estate developers in the UAE and KSA."
    }
    </script>
```

### 2b. Upgrade the existing Speakable `WebPage` (lines 92-94) to `ProfilePage`
WHY: This is a person's profile site; `ProfilePage` with a `mainEntity` pointing at the Person is the schema.org-recommended type for profile/about pages and is what Google's profile-page guidance expects. It keeps the existing speakable behavior. **Replace** the current WebPage block with this.

```html
    <!-- Schema.org: ProfilePage + Speakable -->
    <script type="application/ld+json">
    {
        "@context": "https://schema.org",
        "@type": "ProfilePage",
        "name": "Ahmed Khaire - Founder & CEO, Nogbase",
        "url": "https://ahmedkhaire.com/",
        "dateModified": "2026-07-07",
        "mainEntity": { "@type": "Person", "name": "Ahmed Khaire", "url": "https://ahmedkhaire.com/" },
        "speakable": { "@type": "SpeakableSpecification", "cssSelector": [".hero-card__tagline", ".about-card__lead"] }
    }
    </script>
```

### 2c. Enrich the existing Person schema (lines 33-56) — optional field additions
WHY: `worksFor.sameAs`, `address.addressRegion`, and an explicit `hasOccupation` help entity resolution in Knowledge Graph / AI answers. Add these keys inside the existing Person object (do not create a second Person block).

```jsonc
        // add alongside the existing "worksFor" contents:
        "worksFor": {
            "@type": "Organization",
            "name": "Nogbase",
            "url": "https://nogbase.ae",
            "sameAs": "https://nogbase.ae",
            "description": "A PropTech SaaS platform that transforms how property developers operate."
        },
        // add "addressRegion" to the existing address:
        "address": { "@type": "PostalAddress", "addressLocality": "Dubai", "addressRegion": "Dubai", "addressCountry": "AE" },
        // add as new top-level keys on the Person:
        "hasOccupation": {
            "@type": "Occupation",
            "name": "Founder & CEO",
            "occupationLocation": { "@type": "City", "name": "Dubai" }
        },
        "workLocation": { "@type": "Place", "name": "Dubai, UAE" }
```

---

## 3. FAQPage answer additions — extend the existing `mainEntity` array (lines 79-87)
WHY: These target high-intent buyer queries that UAE/KSA developers actually type into Google and ask AI engines. Add as new `{ "@type": "Question" ... }` objects inside the existing FAQPage array — do not create a second FAQPage. Answers are factual (capabilities already described on the page), no invented claims.

```jsonc
            { "@type": "Question", "name": "What is the best PropTech platform for real estate developers in the UAE?", "acceptedAnswer": { "@type": "Answer", "text": "Nogbase is a B2B PropTech SaaS platform purpose-built for UAE real estate developers. It unifies broker management, sales CRM, AI document generation, payment collections, real-time inventory, and customer handovers in one AI-powered system — replacing the spreadsheets, WhatsApp groups, and siloed CRMs most developers use. It serves developers across Dubai, Abu Dhabi, and the wider UAE and is expanding into Saudi Arabia. Learn more at https://nogbase.ae or contact ahmed@nogbase.ae." } },
            { "@type": "Question", "name": "What broker management software do property developers in Dubai use?", "acceptedAnswer": { "@type": "Answer", "text": "Nogbase provides a dedicated Broker Management Portal for developers in Dubai and the UAE. Brokers self-register, complete KYC and RERA-compliant onboarding, view real-time inventory and pricing, submit EOIs (Expressions of Interest), track deals from expression to booking, and see automated commission calculations and payout tracking — with performance dashboards for both developer and agency. It replaces manual broker management over spreadsheets and WhatsApp." } },
            { "@type": "Question", "name": "Is there a real estate developer CRM for the UAE and Saudi Arabia?", "acceptedAnswer": { "@type": "Answer", "text": "Yes. Nogbase is a real estate developer CRM built for the UAE and expanding into Saudi Arabia (KSA). Unlike generic CRMs, it is designed around developer sales workflows — off-plan inventory, broker networks, EOI-to-booking pipelines, RERA-compliant documentation, and payment plan collections — and was created by Ahmed Khaire, a former PwC Software Solutions Architect who built CRM and broker platforms for Emaar, Aldar, and Majid Al Futtaim." } },
            { "@type": "Question", "name": "How does Nogbase help developers manage off-plan sales and inventory?", "acceptedAnswer": { "@type": "Answer", "text": "Nogbase gives developers real-time inventory management with unit availability and pricing, a 3D inventory viewer, EOI submission and deal tracking from expression to booking, automated payment scheduling and collections, and AI-generated booking and contract documents — so off-plan sales run from a single source of truth instead of disconnected spreadsheets." } }
```

---

## 4. Content / heading recommendations for AEO
These improve extractability by answer engines without changing your design. Ordered by impact.

1. **Add a machine-readable `dateModified` signal.** The page currently has no visible or structured "last updated" date. Adding `dateModified` (done via 2b above) tells AI engines the content is current — freshness is a ranking/citation factor.
2. **Consider one keyword-aligned `<h2>`/`<h3>` phrasing pass.** The Nogbase card `<h2>` reads "The operating system for property developers" (great brand line). AI engines match on literal query phrasing, so ensure the body text near it contains the phrases "PropTech platform for UAE developers", "broker management software", and "real estate developer CRM" — most already appear in the About/Nogbase copy, so this is mostly covered; just confirm "real estate developer CRM" appears verbatim once in visible text.
3. **Keep the FAQ answers self-contained.** AI engines lift single answers out of context, so each answer should name the subject ("Nogbase is…", "Ahmed Khaire is…") rather than "It is…". The existing answers already do this well; the new ones in section 3 follow the same rule.
4. **Add descriptive `alt` text to the two client-logo-only cards if used as evidence** (already good on most images; the client logos have brand-name alts, which is correct — do not stuff keywords).
5. **No `AggregateRating`/`Review` schema** until real reviews exist — flagged again here so it isn't "added for completeness." Fabricated ratings are a manual-action risk and an AEO trust risk.

---

## Cross-file note (already handled by me, not in index.html)
- `robots.txt`: added explicit `Allow` blocks for GPTBot, OAI-SearchBot, ChatGPT-User, ClaudeBot, Claude-Web, anthropic-ai, Claude-SearchBot, PerplexityBot, Perplexity-User, Google-Extended, Applebot(-Extended), Bingbot, DuckDuckBot, Amazonbot, Bytespider, Meta-ExternalAgent, cohere-ai, YouBot.
- `sitemap.xml`: `lastmod` updated to 2026-07-07 on both URLs. CAVEAT: the `https://nogbase.ae/blog` entry is cross-domain; Google ignores cross-domain sitemap URLs unless nogbase.ae is verified in the same Search Console account and reciprocally links the sitemap. It is harmless but not indexed from here — verify nogbase.ae separately for it to count.
- `llms.txt`: expanded with "Who Nogbase is for", an AEO "Common questions" section, and a booking/contact block, following the llms.txt spec (H1 + blockquote summary + H2 sections).
