# Johnson Wright Flooring website: build plan

Status: **draft for approval**. Nothing gets built until this plan is approved.

The site's job is to turn visitors into enquiries and phone calls. Every page ends with a clear way to do both. The main button everywhere reads **Get in contact**.

## 1. Decisions so far

| Area | Decision |
| --- | --- |
| Framework | Next.js (App Router) + TypeScript (strict) + Tailwind CSS. Pages are statically generated. |
| Hosting | Netlify (free plan allows commercial sites). Deploys automatically from `main`. |
| Content editing | Structured files in `content/` (JSON + MDX), edited by Will. No CMS for now. |
| Enquiry form email | Resend: enquiry to the office, confirmation to the customer. |
| Lead backup | Supabase: every enquiry is saved to the database *before* any email is sent. |
| Spam protection | Honeypot field + rate limiting + Cloudflare Turnstile. |
| Analytics | Cloudflare Web Analytics (no cookies, so no consent banner needed). *Assumed, please confirm.* |
| Brand | Existing logo kept. Black and white stay the core colours. |
| Copy rules | No "family-run" wording. No invented facts: anything not yet supplied by the company is a visible placeholder. |
| Client confidentiality | No client names, logos or identifiable venues anywhere on the site, so competitors can't use it to poach clients. Projects are described by type and region only (e.g. "5* hotel, Frankfurt"). Testimonials, if used, are credited by role and sector only. Photos are checked for visible client logos or signage, which get cropped or blurred. |
| Team | No "meet the team" section. |

## 1a. Audit of the old site

http://www.johnsonwright.com was crawled on 19 October 2026. It has 6 pages: `/`, `/work/`, `/where-we-work/`, `/contact/`, `/terms/`, `/privacy-and-cookies/`. Its source code isn't available, so it is replaced, not migrated.

**Stack**: ASP.NET WebForms with jQuery and Modernizr, built by onebestway.

**Problems**

| Area | Issue |
| --- | --- |
| Security | No HTTPS. Browsers mark the site "Not secure", and Google ranks it lower. |
| Mobile | No viewport tag, so phones show a shrunk desktop page. |
| SEO | Empty meta descriptions, no H1 headings, no `robots.txt`, no `sitemap.xml`, no structured data. Titles are just "Work \| Johnson Wright". |
| Accessibility | Most images have empty or wrong alt text (every homepage photo is labelled "Cinema"). |
| Images | Project photos are tiny (largest 385 × 500px) with heavy filters and vignettes. Each image is loaded twice (grey and colour versions). |
| Tracking | Old Google Analytics code (UA-34805963-1). Universal Analytics stopped collecting data in July 2023, so it does nothing. It also contradicts the privacy notice, which says the site uses no cookies. |
| Content | 18 projects all sit on one page in pop-ups, so Google can't index them individually. No service pages. The page of 35 countries is just a list. |
| Contact | Lists a fax number. The enquiry form's handling can't be checked from outside. |

**Keep**
- The welcome copy and the "Setting the standards of customer care" / "International standards, delivered with a local service" wording, minus "family run".
- All 18 project write-ups (brief + what was done). These are strong, specific case studies.
- The construction and accreditations text: CSCS-carded installers with Site Supervisor and First Aid qualifications; CHAS, Constructionline, SafeContractor and Achilles Building Confidence approval. *Company must confirm these are all current.*
- The list of 35 countries worked in.
- Supported charities: MediCinema, St Oswald's Hospice, The Cyrenians. *To confirm.*
- Email: sales@johnsonwright.com. Legal name: Johnson Wright (Flooring) Limited.

**Replace**: every photo (originals needed), the privacy notice (rewrite for the new form, Supabase and Resend), all page structure and code.

## 2. Sitemap

| URL | Page | Purpose |
| --- | --- | --- |
| `/` | Home | What we do and where, Get in contact button, services, projects, trust, areas |
| `/services` | Services overview | All services, links to each service page |
| `/services/[slug]` | One page per service | Key SEO pages (e.g. `/services/carpet-tiles`, `/services/safety-flooring`) |
| `/sectors` | Sectors | Cinemas, hotels, restaurants, kitchens, banks, retail, schools, gyms, heritage |
| `/projects` | Projects gallery | Filter by flooring type and sector; before/after support |
| `/projects/[slug]` | Project case study | Photos, brief, flooring used, size, sector, location |
| `/contractors` | Commercial / contractors | For main contractors, fit-out firms, architects and facilities managers |
| `/areas` | Areas covered | One page. Individual location pages only if there is real content for each. |
| `/about` | About | Company story since 1997, values, accreditations |
| `/contact` | Contact | The enquiry form, phone, email, hours, map link |
| `/faq` | FAQ | Common questions (FAQPage structured data) |
| `/privacy` | Privacy policy | UK GDPR |
| `/terms` | Terms and conditions | Carried over from the old site, after the company confirms it is current |
| `/cookies` | Cookie notice | Explains the site uses no tracking cookies |
| `/contact/thank-you` | Confirmation | After a successful enquiry |
| 404 | Not found | Helpful links back to services and contact |

**301 redirects from the old site** (with and without the trailing slash, and from `http://` and non-`www`):

| Old URL | New URL |
| --- | --- |
| `/work/` | `/projects` |
| `/where-we-work/` | `/areas` |
| `/contact/` | `/contact` |
| `/terms/` | `/terms` |
| `/privacy-and-cookies/` | `/privacy` |
| `/default.aspx` | `/` |

**Projects from the old site** (18, each becomes its own `/projects/[slug]` page): Bespoke hotel fitting, American diner, 5* hotel foyer and staircase, Cinema multiplex (bespoke carpet), 4* city centre hotel staircase, Prestigious clothing store (engineered timber), Cinema multiplex (carpet and vinyl), University physics centre reception, NHS office (5,000m² carpet tiles), 5* hotel in Frankfurt (bespoke Axminster), University premises corridor, Major North East department store, Major London multiplex, Health club chain, Shopping centre in Portugal, Hospice. Plus the Construction and Hotel & Leisure capability write-ups, which feed the Contractors and Sectors pages. All are already anonymous except the physics centre, which is renamed above.

**Sectors** (from the old site's project labels): Hotel & Leisure, Cinema, Restaurant, Retail, Education, Public Sector and Healthcare, Health & Fitness, Construction.

## 3. Page outlines

**Home**
1. Hero: headline naming the work and the place ("Commercial flooring contractors in Gateshead and the North East"), *Get in contact* button, tap-to-call number, large project photo.
2. Proof strip: since 1997, sectors served, worldwide projects, accreditations (when supplied).
3. Services overview: a featured layout, not a uniform card grid.
4. Recent projects: 3 to 4 large photos.
5. Sectors served.
6. Google reviews or testimonials (real only, credited by role and sector, never by client name).
7. Areas covered.
8. Get in contact band.

**Service page** (one template, content from `content/services/*.mdx`)
Hero photo, what it is, where it suits (sectors), benefits, products/brands used, related projects, FAQs for that service, Get in contact button. Service and BreadcrumbList structured data.

**Projects**: filter chips (flooring type, sector), responsive image grid, case-study pages with before/after slider where pairs exist.

**Contractors**: how the company works with main contractors (programme, out-of-hours working, RAMS, accreditations, insurance), relevant projects, direct contact.

**About**: story, values, premises photo, accreditations, supported charities.

**Contact**: enquiry form (section 5), phone, email, hours, map link.

## 4. Components

`Button`, `Section`, `Container`, `Heading`, `Eyebrow`, `ServiceCard`, `ServiceFeature`, `ProjectCard`, `ProjectGallery` + `FilterChips`, `BeforeAfter`, `Testimonial`, `ReviewSummary`, `TrustStrip`, `CtaBand`, `EnquiryForm`, `ContactDetails`, `OpeningHours`, `Breadcrumbs`, `SiteHeader`, `SiteFooter`, `MobileActionBar` (sticky Call / Get in contact bar on phones), `Placeholder` (visible marker for missing text or images, so nothing invented ships by accident).

Business details (name, address, phone, hours) live in one file, `content/business.ts`, and every page reads from it. That keeps name, address and phone identical everywhere, which Google checks.

## 5. Enquiry form and flow

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

Both directions: mobile-first, sticky Call / Get in contact bar on phones, 48px minimum tap targets, subtle motion that switches off for people who prefer reduced motion.

## 7. Standards

- **Accessibility**: WCAG 2.2 AA. Semantic HTML, keyboard navigation, visible focus, 4.5:1 text contrast, labelled fields, alt text on every image, errors announced to screen readers.
- **Performance**: Core Web Vitals green on mobile. `next/image` (AVIF/WebP, correct sizes, lazy-loading below the fold), self-hosted fonts via `next/font` with `font-display: swap`, minimal client JavaScript.
- **SEO**: unique title and description per page, one H1 per page, clean URLs, `sitemap.xml`, `robots.txt`, canonical tags, Open Graph images, JSON-LD (`HomeAndConstructionBusiness`, `Service`, `FAQPage`, `BreadcrumbList`), 301 redirects from old URLs.
- **Security**: CSP, HSTS, X-Content-Type-Options, Referrer-Policy and Permissions-Policy headers via `netlify.toml`; server-side validation; secrets in environment variables only; Dependabot for dependency updates.
- **Privacy**: collect only what quoting needs, enquiries deleted after a set period (suggest 24 months, to confirm with the company), privacy policy and cookie notice, no tracking cookies.
- **Code quality**: TypeScript strict, ESLint + Prettier, clear folders, no dead code, README covering setup, environment variables, deployment and how to edit content.
- **Testing**: Playwright end-to-end tests (navigation, enquiry form happy path and errors, mobile bar), Lighthouse CI targeting 95+ on mobile for Performance, Accessibility, Best Practices and SEO. GitHub Actions runs lint, type-check, tests and build on every push.

## 8. Folder structure

```
app/                 routes (one folder per page)
components/          reusable UI components
content/             business.ts, services/*.mdx, projects/*.mdx, sectors.json, faqs.json, testimonials.json (no client names)
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
| 4. Enquiry form | Form, Supabase table and storage, Resend emails, Turnstile, rate limiting, thank-you page, privacy and cookie pages | Test enquiry |
| 5. SEO and quality | Metadata, JSON-LD, sitemap, OG images, redirects, analytics, headers, accessibility and Lighthouse fixes, full test suite | Lighthouse report |
| 6. Content and launch | Company content and photos added, placeholders removed, launch checklist | **Go live** |

## 10. What's needed to proceed

**From you (accounts, all free tiers):**
- Push access for Claude to this GitHub repo (install the Claude GitHub App).
- Netlify account connected to the repo.
- Supabase project (later, phase 4).
- Resend account (phase 4), plus access to the domain's DNS to verify sending.
- Cloudflare account for Turnstile and Web Analytics (phase 4/5).
**From the company:** the information request document. The old site already answered some of it (email, projects, countries, accreditations, charities), so those only need confirming. The biggest gap is **original, full-size project photos**. The old site's copies are too small and too heavily filtered to use beyond temporary placeholders.
