# SEO Diagnosis: New Worknoon Website Not Indexing After Sitemap Submission

## Scenario
A new Worknoon website has been launched and the sitemap has been submitted to
Google Search Console, but pages are not appearing in Google's index. This document
outlines a structured troubleshooting approach to diagnose and resolve the issue.

---

## 1. Crawlability Tests

The first step is to confirm whether Google can even access the site.

### Google Search Console — Coverage Report
- Navigate to **Index > Pages** in GSC
- Look for pages under "Not indexed" and check the reason codes:
  - *Crawled — currently not indexed* → Google visited but chose not to index
  - *Discovered — currently not indexed* → Google found the URL but hasn't crawled it yet
  - *Blocked by robots.txt* → Crawling is being blocked (see Section 3)
  - *Excluded by noindex tag* → Pages are explicitly excluded (see Section 3)

### Manual Crawl Test
- Use the **URL Inspection Tool** in GSC on the homepage and key pages
- Click "Test Live URL" to see what Googlebot sees in real time
- Check for: blocked resources, render errors, or redirect issues

### Third-Party Crawl Tools
- Run a crawl using **Screaming Frog** or **Ahrefs Site Audit**
- Look for: broken internal links, redirect chains, orphaned pages (no internal
  links pointing to them), and pages returning non-200 status codes

---

## 2. Canonical Checks

Canonical tags tell Google which version of a URL is the "official" one. Incorrect
canonicals are a common reason pages don't get indexed.

### What to Check
- Open the page source (`Ctrl+U`) and search for `<link rel="canonical"`
- The canonical tag should point to the exact URL of the page itself — not the
  homepage, not a different URL variant
- Common mistakes on WordPress/Elementor builds:
  - All pages canonicalizing to the homepage
  - HTTP canonical on an HTTPS page
  - Trailing slash inconsistency (`/about` vs `/about/`)

### How to Fix
- In **Rank Math** or **Yoast SEO**, check the canonical URL field per page
- Ensure the WordPress Site URL in **Settings > General** uses HTTPS and matches
  the canonical domain exactly

---

## 3. Robots.txt & Noindex Audit

### Robots.txt
- Visit `https://worknoon.com/robots.txt` directly in a browser
- Confirm it does NOT contain:
  ```
  User-agent: *
  Disallow: /
  ```
  This line blocks all crawlers from the entire site — a common mistake on newly
  launched WordPress sites that were set to "discourage search engines" during
  development

- The correct robots.txt for a live site should allow crawling and point to the
  sitemap:
  ```
  User-agent: *
  Disallow:
  Sitemap: https://worknoon.com/sitemap.xml
  ```

- Also check in WordPress: **Settings > Reading** — ensure "Discourage search
  engines from indexing this site" is **unchecked**

### Noindex Tags
- Use Screaming Frog or the URL Inspection Tool to scan all pages for:
  ```html
  <meta name="robots" content="noindex">
  ```
- In Rank Math: **SEO > Titles & Meta > Global Meta** — confirm noindex is not
  enabled globally
- Check individual page settings — Elementor and page builders sometimes add
  their own meta overrides

---

## 4. Sitemap Structure Issues

A badly structured sitemap can prevent Google from discovering and prioritizing pages.

### Validation Checks
- Visit `https://worknoon.com/sitemap.xml` directly — it should load cleanly
  with no XML errors
- Submit the sitemap URL in **GSC > Sitemaps** and check the status:
  - *Success* → Sitemap is readable
  - *Couldn't fetch* → Server is blocking Googlebot
  - *Has errors* → Malformed XML, bad URLs, or HTTP links on HTTPS site

### Common Sitemap Problems on WordPress
- Sitemap includes `noindex` pages (tag pages, author pages, attachment pages)
- Sitemap URLs use HTTP instead of HTTPS
- Sitemap is not being regenerated after new pages are published
- WordPress caching plugin is serving a stale sitemap

### Fix
- Use **Rank Math** or **Yoast SEO** to regenerate the sitemap
- Exclude non-essential pages (privacy policy, thank-you pages, etc.) from the
  sitemap if they add no indexing value
- After fixing, resubmit in GSC and click "Validate Fix"

---

## 5. Page Speed Indexing Blockers

Google's crawler has a crawl budget — slow pages consume more of it and get crawled
less frequently. Very slow or broken pages may be deprioritized entirely.

### What to Test
- Run the homepage and key pages through **Google PageSpeed Insights**
  (https://pagespeed.web.dev)
- Check **Core Web Vitals** in GSC under **Experience > Core Web Vitals**
- Look for: render-blocking JavaScript, unoptimized images, slow server response
  time (TTFB > 600ms)

### Common WordPress Speed Issues That Block Indexing
- Large uncompressed images (especially from Elementor full-width sections)
- No caching plugin configured (WP Rocket, W3 Total Cache, or LiteSpeed Cache)
- Hosting on a slow shared server with no CDN
- Unused CSS/JS from multiple plugins loading on every page

### Fix
- Install **WP Rocket** or **LiteSpeed Cache** for caching and minification
- Use **Smush** or **ShortPixel** for image compression
- Enable a CDN (Cloudflare free tier is sufficient for a new site)
- Check that the server returns a 200 status code quickly using:
  `curl -o /dev/null -s -w "%{time_total}" https://worknoon.com`

---

## 6. Search Console Debugging Steps

Once the above checks are done, use GSC systematically to validate fixes.

### Step-by-Step GSC Debugging Workflow

1. **URL Inspection Tool**
   - Test the homepage first: paste `https://worknoon.com` into the inspection bar
   - Click "Test Live URL" — review what Googlebot sees
   - Check: indexing allowed? canonical correct? page rendered correctly?

2. **Request Indexing**
   - After confirming no blockers, click "Request Indexing" for key pages
   - Do this for: homepage, about page, and 3–5 core job listing pages

3. **Coverage Report**
   - Go to **Index > Pages** and filter by "Not indexed"
   - Address each error reason systematically before re-requesting indexing

4. **Sitemap Re-submission**
   - Delete the existing sitemap entry in GSC and re-add it
   - Wait 24–48 hours and check the "Sitemaps" panel for a fresh status

5. **Rich Results Test**
   - Run key pages through https://search.google.com/test/rich-results
   - Ensure schema markup is being detected without errors — schema errors can
     negatively affect how Google processes a page

6. **Manual Actions Check**
   - Go to **Security & Manual Actions > Manual Actions**
   - Confirm there are no manual penalties applied to the site (common for very
     new sites flagged as thin content)

7. **Links Report**
   - Go to **Links** in GSC
   - A brand new site with zero external links pointing to it will naturally be
     slow to index — build at least 3–5 quality backlinks (from social profiles,
     directories, and the founder's own website) to accelerate discovery

---

## Summary Checklist

| Check | Tool | Expected Result |
|-------|------|-----------------|
| Robots.txt not blocking | Browser / GSC | `Disallow:` is empty |
| Noindex not set globally | Rank Math / GSC | No global noindex |
| Canonical tags correct | Page source / Screaming Frog | Self-referencing canonicals |
| Sitemap loads cleanly | Browser / GSC Sitemaps | Valid XML, HTTPS URLs |
| Page speed acceptable | PageSpeed Insights | LCP < 2.5s, no critical errors |
| GSC shows no manual action | GSC Manual Actions | No issues detected |
| At least one external link | GSC Links | 1+ external referring domain |
