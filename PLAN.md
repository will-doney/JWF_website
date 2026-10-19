# Johnson Wright Flooring website: build plan

Status: **draft for approval**. Nothing gets built until this plan is approved.

The site's job is to turn visitors into quote requests and phone calls. Every page ends with a clear way to do both.

## 1. Decisions so far

| Area | Decision |
| --- | --- |
| Framework | Next.js (App Router) + TypeScript (strict) + Tailwind CSS. Pages are statically generated. |
| Hosting | Netlify (free plan allows commercial sites). Deploys automatically from `main`. |
| Content editing | Structured files in `content/` (JSON + MDX), edited by Will. No CMS for now. |
| Quote form email | Resend: enquiry to the office, confirmation to the customer. |
| Lead backup | Supabase: every enquiry is saved to the database *before* any email is sent. |
| Spam protection | Honeypot field + rate limiting + Cloudflare Turnstile. |
| Analytics | Cloudflare Web Analytics (no cookies, so no consent banner needed). *Assumed, please confirm.* |
| Brand | Existing logo kept. Black and white stay the core colours. |
| Copy rules | No "family-run" wording. No invented facts: anything not yet supplied by the company is a visible placeholder. |

The old site (http://www.johnsonwright.com) can't be reached from the build environment, and its code isn't available. It is replaced, not migrated. Its wording is reused where the company approves it.

## 2. Sitemap

| URL | Page | Purpose |
| --- | --- | --- |
| `/` | Home | What we do and where, quote CTA, services, projects, trust, areas |
| `/services` | Services overview | All services, links to each service page |
| `/services/[slug]` | One page per service | Key SEO pages (e.g. `/services/carpet-tiles`, `/services/safety-flooring`) |
| `/sectors` | Sectors | Cinemas, hotels, restaurants, kitchens, banks, retail, schools, gyms, heritage |
| `/projects` | Projects gallery | Filter by flooring type and sector; before/after support |
| `/projects/[slug]` | Project case study | Photos, brief, flooring used, size, sector, location |
| `/contractors` | Commercial / contractors | For main contractors, fit-out firms, architects and facilities managers |
| `/areas` | Areas covered | One page. Individual location pages only if there is real content for each. |
| `/about` | About | Company story since 1997, team, values |
| `/quote` | Quote request | The full quote form |
| `/contact` | Contact | Map link, hours, phone, email, short form |
| `/faq` | FAQ | Common questions (FAQPage structured data) |
| `/privacy` | Privacy policy | UK GDPR |
| `/cookies` | Cookie notice | Explains the site uses no tracking cookies |
| `/quote/thank-you` | Confirmation | After a successful enquiry |
| 404 | Not found | Helpful links back to services and quote |

Old URLs get 301 redirects to their nearest new page. The old URL list is still needed (see section 9).

## 3. Page outlines

**Home**
1. Hero: headline naming the work and the place ("Commercial flooring contractors in Gateshead and the North East"), *Get a free quote* button, tap-to-call number, large project photo.
2. Proof strip: since 1997, sectors served, worldwide projects, accreditations (when supplied).
3. Services overview: a featured layout, not a uniform card grid.
4. Recent projects: 3 to 4 large photos.
5. Sectors served.
6. Testimonial or Google reviews (real only).
7. Areas covered.
8. Quote CTA band.

**Service page** (one template, content from `content/services/*.mdx`)
Hero photo, what it is, where it suits (sectors), benefits, products/brands used, related projects, FAQs for that service, quote CTA. Service and BreadcrumbList structured data.

**Projects**: filter chips (flooring type, sector), responsive image grid, case-study pages with before/after slider where pairs exist.

**Contractors**: how the company works with main contractors (programme, out-of-hours working, RAMS, accreditations, insurance), relevant projects, direct contact.

**About**: story, team grid, premises photo, accreditations.

**Quote form**: see section 5.

## 4. Components

`Button`, `Section`, `Container`, `Heading`, `Eyebrow`, `ServiceCard`, `ServiceFeature`, `ProjectCard`, `ProjectGallery` + `FilterChips`, `BeforeAfter`, `Testimonial`, `ReviewSummary`, `TrustStrip`, `CtaBand`, `QuoteForm`, `ContactDetails`, `OpeningHours`, `Breadcrumbs`, `SiteHeader`, `SiteFooter`, `MobileActionBar` (sticky call/quote bar on phones), `Placeholder` (visible marker for missing text or images, so nothing invented ships by accident).

Business details (name, address, phone, hours) live in one file, `content/business.ts`, and every page reads from it. That keeps name, address and phone identical everywhere, which Google checks.

## 5. Quote form and enquiry flow

**Fields**: name, email, phone, preferred contact method, company (optional), site postcode, property/sector type, flooring type(s), approximate area (m²) or number of rooms, timescale, message, optional photos/plans (up to 3 files, 10 MB each, images or PDF), consent checkbox for the privacy policy.

**What happens on submit**
1. The browser checks the fields and shows clear, accessible error messages.
2. The server checks everything again with the same rules (shared Zod schema).
3. Spam checks: the honeypot field must be empty, the Turnstile token must verify, and each visitor is limited to 5 enquiries per hour (counted in Supabase against a hashed IP).
4. The enquiry is **saved to Supabase first**. Uploaded files go to a private Supabase Storage bucket.
5. Resend emails the office the full enquiry, with secure time-limited links to any files.
6. Resend emails the customer a confirmation.
7. The saved enquiry is marked with whether each email was sent. If an email fails, the lead is still safe in the database, and the customer still sees the thank-you page.

Secrets (Supabase service key, Resend key, Turnstile secret) live only in Netlify environment variables, never in the code or browser.

## 6. Design

Two directions are mocked up for you to pick from before the homepage is built. Whichever you choose becomes the design tokens (CSS variables + Tailwind theme).

**A. Monochrome Precision**: strict black and white, true to the current brand. Hairline rules, square corners, no shadows, a confident grotesque heading face, large photography doing the colour work.
- Colours: ink `#0D0D0D`, paper `#FFFFFF`, stone `#F3F3F1`, rule `#DCDCD8`, muted `#5C5C58`; accent is black itself.
- Type: Archivo (headings) + Source Sans 3 (body) + IBM Plex Mono (small labels).
- Spacing: 4px base, scale 4 / 8 / 12 / 16 / 24 / 32 / 48 / 64 / 96 / 128. Radius 0. Shadows none.

**B. Material and Warmth**: black and white kept for the logo, type and buttons, set on warm neutrals taken from oak, limestone and wool, with one deep green accent for calls to action.
- Colours: limestone `#ECE8E1`, paper `#FAF8F5`, charcoal `#1C1B19`, oak `#9A7550`, accent green `#1F4D3A`.
- Type: Schibsted Grotesk (headings) + Public Sans (body).
- Spacing: same scale. Radius 2px. One soft shadow for raised elements only.

Both directions: mobile-first, sticky call/quote bar on phones, 48px minimum tap targets, subtle motion that switches off for people who prefer reduced motion.

## 7. Standards

- **Accessibility**: WCAG 2.2 AA. Semantic HTML, keyboard navigation, visible focus, 4.5:1 text contrast, labelled fields, alt text on every image, errors announced to screen readers.
- **Performance**: Core Web Vitals green on mobile. `next/image` (AVIF/WebP, correct sizes, lazy-loading below the fold), self-hosted fonts via `next/font` with `font-display: swap`, minimal client JavaScript.
- **SEO**: unique title and description per page, one H1 per page, clean URLs, `sitemap.xml`, `robots.txt`, canonical tags, Open Graph images, JSON-LD (`HomeAndConstructionBusiness`, `Service`, `FAQPage`, `BreadcrumbList`), 301 redirects from old URLs.
- **Security**: CSP, HSTS, X-Content-Type-Options, Referrer-Policy and Permissions-Policy headers via `netlify.toml`; server-side validation; secrets in environment variables only; Dependabot for dependency updates.
- **Privacy**: collect only what quoting needs, enquiries deleted after a set period (suggest 24 months, to confirm with the company), privacy policy and cookie notice, no tracking cookies.
- **Code quality**: TypeScript strict, ESLint + Prettier, clear folders, no dead code, README covering setup, environment variables, deployment and how to edit content.
- **Testing**: Playwright end-to-end tests (navigation, quote form happy path and errors, mobile bar), Lighthouse CI targeting 95+ on mobile for Performance, Accessibility, Best Practices and SEO. GitHub Actions runs lint, type-check, tests and build on every push.

## 8. Folder structure

```
app/                 routes (one folder per page)
components/          reusable UI components
content/             business.ts, services/*.mdx, projects/*.mdx, sectors.json, faqs.json, testimonials.json
lib/                 validation schema, Supabase, Resend, Turnstile, SEO helpers
public/              logo, icons, project photos
tests/               Playwright tests
supabase/            database schema (SQL migration)
netlify.toml         build settings, headers, redirects
```

## 9. Phases

Each phase ends with lint, type-check, tests and a production build passing, then a commit.

| Phase | Work | You review |
| --- | --- | --- |
| 0. Setup | Next.js project, TypeScript strict, Tailwind, ESLint/Prettier, Playwright, GitHub Actions, `netlify.toml`, README outline | Nothing |
| 1. Design | Both design directions mocked up; the chosen one turned into tokens and core components | **Pick a direction** |
| 2. Homepage | Full homepage with placeholders and mobile bar | **Approve homepage** |
| 3. Pages | All remaining pages and templates, content files, placeholders | Pages |
| 4. Quote form | Form, Supabase table and storage, Resend emails, Turnstile, rate limiting, thank-you page, privacy and cookie pages | Test enquiry |
| 5. SEO and quality | Metadata, JSON-LD, sitemap, OG images, redirects, analytics, headers, accessibility and Lighthouse fixes, full test suite | Lighthouse report |
| 6. Content and launch | Company content and photos added, placeholders removed, launch checklist | **Go live** |

## 10. What's needed to proceed

**From you (accounts, all free tiers):**
- Push access for Claude to this GitHub repo (install the Claude GitHub App).
- Netlify account connected to the repo.
- Supabase project (later, phase 4).
- Resend account (phase 4), plus access to the domain's DNS to verify sending.
- Cloudflare account for Turnstile and Web Analytics (phase 4/5).
- A list of the old site's page addresses for redirects, or allow `www.johnsonwright.com` in this environment's network settings so I can read it.

**From the company:** the information request document (contact email, services, projects, photos, accreditations, reviews, domain access). Placeholders cover anything missing until it arrives.
