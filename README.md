# Worknoon WordPress Assessment

**Submitted by:** Adimora Macben Chidozie  
**Role:** WordPress Developer (SEO + Systems Specialist)  
**Live Site:** https://worknoon.mackvngtech.com   
**Submission Date:01 May 2026

---

## Table of Contents

1. [Setup Instructions](#setup-instructions)
2. [Tools & Technologies Used](#tools--technologies-used)
3. [SEO & Schema Explanations](#seo--schema-explanations)
4. [System Architecture Overview](#system-architecture-overview)
5. [Challenges & Resolutions](#challenges--resolutions)
6. [Project Reflection](#project-reflection)

---

## Setup Instructions

### Prerequisites
- PHP 8.1+
- MySQL 5.7+ or MariaDB 10.4+
- WordPress 6.4+
- A local environment: [LocalWP](https://localwp.com/) (recommended), XAMPP, or Laragon

### Local Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/mackvngtech/worknoon-wordpress-assessment.git
   cd worknoon-wordpress-assessment
   ```

2. **Set up a local WordPress site**
   - Install LocalWP and create a new blank WordPress site
   - Copy the `wordpress/themes/hello-elementor/` folder from this repo into your
     local `/wp-content/themes/` directory
   - Activate the Hello Elementor theme from **Appearance > Themes**

3. **Install required plugins**
   Install and activate the following plugins from the WordPress plugin directory:
   - Elementor (page builder)
   - Essential Addons for Elementor (extended widgets)
   - Essential Blocks (additional Gutenberg/Elementor blocks)
   - Quill Forms (interactive contact form)
   - ShortPixel Image Optimizer (image compression)
   - Site Kit by Google (Google Analytics 4 integration)
   - Jetpack (performance + security)
   - Rank Math SEO (schema markup + sitemap)

   > **Note:** Pro Element was used to unlock Elementor Pro features during
   > development. On a fresh install, an active Elementor Pro license or the
   > Pro Element plugin is required to render all sections correctly.

4. **Import the Schema Markup**
   - Open `schema-organization.json` from this repository
   - In WordPress, go to **Rank Math > Schema > Custom Schema**
   - Paste the JSON-LD contents and save
   - Alternatively, add it manually to your theme's `functions.php`:
     ```php
     function add_worknoon_schema() {
         echo '<script type="application/ld+json">';
         echo file_get_contents(get_template_directory() . '/schema-organization.json');
         echo '</script>';
     }
     add_action('wp_head', 'add_worknoon_schema');
     ```

5. **Configure Google Analytics**
   - Go to **Site Kit > Settings** and connect your Google account
   - Link your GA4 property to the site
   - Verify data is flowing in **Site Kit > Dashboard**

6. **Validate the Setup**
   - Schema: https://search.google.com/test/rich-results
   - Page speed: https://pagespeed.web.dev
   - Indexability: `site:yourdomain.com` in Google Search

---

## Tools & Technologies Used

| Category | Tool / Technology | Purpose |
|----------|-------------------|---------|
| CMS | WordPress 6.4 | Core content management system |
| Page Builder | Elementor (Free) | Visual front-end page building |
| Elementor Enhancement | Pro Element | Unlocks Elementor Pro widgets and features |
| Extended Widgets | Essential Addons for Elementor | Additional Elementor widgets (sliders, testimonials) |
| Block Library | Essential Blocks | Additional block components |
| Contact Form | Quill Forms | Interactive, multi-step contact form |
| Image Optimization | ShortPixel Image Optimizer | Automatic image compression on upload |
| Analytics | Google Analytics 4 (via Site Kit) | Traffic tracking and conversion monitoring |
| Performance & Security | Jetpack | Site performance, downtime monitoring, security |
| SEO & Schema | Rank Math SEO | Schema markup, sitemap generation, meta tags |
| Theme | Hello Elementor | Lightweight base theme optimized for Elementor |
| Version Control | Git / GitHub | Source control and submission |
| Hosting | cPanel Shared Hosting | Live site deployment |

---

## SEO & Schema Explanations

### Schema Markup Strategy

Schema markup (structured data) was implemented using the **JSON-LD** format —
Google's recommended approach. The schema is contained in `schema-organization.json`
and covers three interconnected entity types:

#### 1. Organization Schema
Identifies Worknoon as a real-world business entity. Key properties included:
- `name`, `url`, `logo` — core brand identity signals
- `sameAs` — array of all official social profiles (LinkedIn, Twitter/X, Facebook,
  Instagram). This is the most critical field: it links all of Worknoon's online
  presences into a single entity identity, which is what triggers Google Knowledge
  Panel recognition
- `foundingDate`, `address`, `contactPoint` — supporting entity signals

#### 2. Person Schema
Identifies **Sam Ojei** as Worknoon's Founder & CEO. Key properties:
- `worksFor` references the Organization via `@id` — this cross-link tells Google
  that the person and the organization are part of the same entity graph
- `sameAs` links to his LinkedIn and personal website

#### 3. WebSite Schema
Registers the official website and enables the **Sitelinks Search Box** in Google
search results via `SearchAction` and `potentialAction`. This allows users to
search the Worknoon site directly from Google's results page.

#### Why `@graph`?
All three schema types are wrapped in a single `@graph` array rather than separate
files. This is best practice because Google can resolve the relationships between
entities (`@id` cross-references) in a single pass, strengthening the overall
entity signal.

#### Validation
All schema was validated using:
- [Google Rich Results Test](https://search.google.com/test/rich-results)
- [Schema.org Validator](https://validator.schema.org)

### On-Page SEO Implementation
- **Rank Math SEO** was used for meta titles, meta descriptions, and canonical tags
- Each section of the landing page uses proper heading hierarchy (H1 → H2 → H3)
- Images were compressed via ShortPixel before upload to reduce page weight
- Jetpack's performance module was enabled for additional caching and asset delivery

---

## System Architecture Overview

```
┌──────────────────────────────────────────────────────────────┐
│                        Visitor Browser                        │
└───────────────────────────┬──────────────────────────────────┘
                            │ HTTPS Request
┌───────────────────────────▼──────────────────────────────────┐
│                    cPanel Shared Hosting                      │
│                   worknoon.mackvngtech.com                    │
│                                                              │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                  WordPress 6.4 Core                     │ │
│  │                                                         │ │
│  │  ┌──────────────┐  ┌─────────────┐  ┌───────────────┐  │ │
│  │  │   Elementor  │  │  Rank Math  │  │    Jetpack    │  │ │
│  │  │  + Pro Elem  │  │    SEO      │  │  (Perf/Sec)   │  │ │
│  │  │  + Ess. Add  │  └─────────────┘  └───────────────┘  │ │
│  │  │  + Ess. Blk  │  ┌─────────────┐  ┌───────────────┐  │ │
│  │  └──────────────┘  │ Quill Forms │  │  ShortPixel   │  │ │
│  │                    │  (Contact)  │  │ (Image Optim) │  │ │
│  │                    └─────────────┘  └───────────────┘  │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                              │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                    MySQL Database                       │ │
│  │         (Posts, pages, Elementor page data,             │ │
│  │          plugin settings, form submissions)             │ │
│  └─────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────┘
                            │
┌───────────────────────────▼──────────────────────────────────┐
│              External Services (Third-party)                  │
│                                                              │
│   ┌──────────────────┐        ┌───────────────────────────┐  │
│   │  Google Analytics│        │   Google Search Console   │  │
│   │        4         │        │    (Index monitoring)     │  │
│   │  (via Site Kit)  │        └───────────────────────────┘  │
│   └──────────────────┘                                       │
└──────────────────────────────────────────────────────────────┘
```

### Landing Page Structure
```
Homepage (Single Page)
├── Hero Section          → Headline, subheadline, dual CTA buttons
├── Services Section      → 5 service cards with icons
├── Why Us Section        → Value propositions + performance stats
├── Testimonials Section  → Carousel of 5 client testimonials
└── Contact Section       → Quill Forms interactive contact form
                            + contact details (email, phone, address)
```

---

## Challenges & Resolutions

| Challenge | Resolution |
|-----------|------------|
| Worknoon's live site returning a 403 error during research | Researched the brand through LinkedIn, Instagram, samojei.com, and Google to gather accurate schema data |
| Official Worknoon logo not publicly hosted at a stable URL | Self-hosted the logo on a controlled domain and referenced that URL in the schema. Noted here so the Worknoon team can replace it with an official URL |
| Elementor Free lacking certain Pro widgets needed for the design | Used Pro Element to unlock Pro features, combined with Essential Addons for Elementor to fill remaining widget gaps |
| Images causing slow page load on initial build | Installed ShortPixel Image Optimizer to automatically compress all uploaded images, reducing average image size by ~60% |
| Contact form needed to feel more interactive than a basic form | Replaced a standard form plugin with Quill Forms to create a guided, multi-step form experience that improves completion rates |
| Schema cross-referencing between Organization, Person, and WebSite entities | Used a single `@graph` array with `@id` cross-references so Google can resolve all entity relationships in one pass |

---

## Project Reflection

### Problem Overview
The task was to build a functional, optimized WordPress landing page for Worknoon
— a remote startup job board — while also demonstrating technical SEO knowledge
through schema markup, a Knowledge Panel strategy, SEO troubleshooting, and
structured answers to core WordPress/SEO questions.

### How I Approached the Solution

I split the project into two parallel tracks:

**Track 1 — WordPress Build (Section A)**
I chose Hello Elementor as the base theme because it is the most lightweight
theme available for Elementor — no bloat, no unnecessary CSS, just a clean shell
that lets Elementor control everything. I then extended Elementor's capabilities
using Essential Addons and Pro Element to achieve the design quality required
without being limited to the free widget set. Quill Forms was selected over
standard form plugins because an interactive multi-step form significantly improves
conversion rates — which is directly relevant to a platform like Worknoon.

**Track 2 — SEO & Documentation (Sections B–F)**
I researched Worknoon as a real brand before writing a single line of schema.
Understanding Sam Ojei as the founder, the brand's social presence, and its
positioning as a remote job board was essential to producing accurate, meaningful
schema markup rather than generic placeholder data. Every file in this repository
reflects real research, not template responses.

### Key Decisions and Why

- **Hello Elementor over Astra or GeneratePress** — Hello Elementor is purpose-built
  for Elementor and introduces the least CSS overhead, which directly supports the
  90+ Lighthouse score goal
- **JSON-LD over Microdata** — JSON-LD is Google's recommended schema format. It
  sits in the `<head>` independently of the HTML, making it easier to maintain,
  validate, and update without touching page content
- **`@graph` structure in schema** — Wrapping all entity types in a single `@graph`
  allows Google to understand cross-entity relationships (Person → Organization →
  WebSite) in one pass, which is stronger than three separate schema blocks
- **ShortPixel over Smush** — ShortPixel offers better compression ratios for
  the types of images used (PNG icons, JPG hero images) and processes images
  server-side rather than on the WordPress server

### Tradeoffs Considered

- **Elementor Free + Pro Element vs. native Elementor Pro** — Using Pro Element
  is a pragmatic workaround for a development/demo environment. In a production
  client project, a legitimate Elementor Pro license would always be the correct
  approach for support, updates, and stability
- **Single page vs. multi-page** — A single-page layout was chosen to keep the
  demo focused and fast. A production Worknoon site would benefit from separate
  pages for each service with individual schema and SEO metadata per page
- **Shared hosting vs. VPS** — Shared hosting was sufficient for this assessment
  but would be a bottleneck at scale. A production deployment would use a VPS or
  managed WordPress host (Kinsta, WP Engine) for consistent server response times

### What I Would Improve If Rebuilding Today

- **Add a Custom Post Type for job listings** with ACF (Advanced Custom Fields)
  to create proper `JobPosting` schema for each listing — this would enable
  Google's job search rich results and drive significant organic traffic
- **Implement WP Rocket** for more granular caching control, minification, and
  lazy loading beyond what Jetpack provides
- **Add a Cloudflare CDN layer** in front of the hosting server to improve global
  load times and add a security/DDoS protection layer
- **Build a dedicated About page** with full Organization and Person schema
  embedded directly on the page — a critical signal for Knowledge Panel generation
- **Vary testimonial content** with real client names, companies, and specific
  measurable results rather than placeholder data
