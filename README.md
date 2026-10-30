# MOR website: static deploy

A plain static site: no build step, no server, no database. Upload this folder's contents to the web root.

## Addresses
Every page has a real address and its own file:

| Address | File |
|---|---|
| `/` | `index.html` |
| `/about-us` | `about-us.html` |
| `/pricing` | `pricing.html` |
| `/trading-and-demat-with-mor` | `trading-and-demat-with-mor.html` |
| `/reference_documents` | `reference_documents.html` |
| `/disclaimer` | `disclaimer.html` |
| `/privacy-policy` | `privacy-policy.html` |
| `/terms-and-conditions` | `terms-and-conditions.html` |

Each file carries the whole site, with that page already open and its own `<title>`, description, canonical link and social tags, so search engines and first paint get the right page. Links inside the site switch pages without a reload (History API); opening an address directly, or reloading, loads its file.

`/faqs` (the old FAQ page) redirects to the FAQ section of the home page. `/blogs` is not part of this build and returns the 404 page.

## Run it locally
Any static server that serves `about-us.html` at `/about-us` (clean URLs). For example: `npx serve .` (clean URLs are on by default).

## Deploy
- **Vercel:** import the folder as a static project (framework "Other", no build command, output directory `.`). `vercel.json` turns on clean URLs, rewrites each address to its file, and carries the redirects and headers.
- **Netlify / Cloudflare Pages:** publish this folder (no build command). Both serve `about-us.html` at `/about-us` out of the box; `_redirects` is used as-is. If a page 404s, enable Pretty/Clean URLs.
- **Anything else (nginx, S3 + CloudFront, Apache):** serve the folder with `try_files $uri $uri.html /index.html` style clean URLs, recreate the redirects in `_redirects` in that server's syntax, and set the cache headers from `_headers`.
- HTTPS and gzip/brotli compression on (each page is about 870 KB uncompressed, the 3D phone model about 2 MB).

## The domain
The canonical origin is set to `https://mor.trade` (the old site's address, no `www`). It appears in every page's canonical link, `og:url`, `sitemap.xml` and `robots.txt`. If the final domain differs, change `SITE` in the build (`routes.py`) or search-and-replace it and rebuild. Make the no-`www` address primary and redirect `www` to it. When switching DNS, change only the website records and leave the MX and TXT records alone, or support@mor.trade stops receiving mail.

## Before launch (open items)
1. **Check the legal addresses.** `/pricing`, `/about-us`, `/reference_documents` and `/trading-and-demat-with-mor` match the old site. The old addresses of Terms, Privacy and Disclaimer are unknown, so `/terms-and-conditions`, `/privacy-policy` and `/disclaimer` are a best guess. Check where the App Store and Play Store listings, emails and the app link to, and add a redirect (`_redirects` / `vercel.json`) for any other address.
2. **Anything that used the old site's backend** (`/api/...`, forms, the old waitlist button, callbacks) is gone. Confirm nothing outside the site depended on it.
3. **Social sharing.** Add an `og:image` (1200 x 630, absolute https URL) to each page's `<head>`.
4. **Analytics / cookie banner** if used. None is included.
5. **Fees wording.** The Trading & Demat page says "zero AMC or platform fees" (copied from the old page) while Pricing says "₹0 for first year". Compliance should confirm which is right, and that the Terms, Privacy, Disclaimer and Pricing text matches the currently approved versions.
6. **After the switch,** open every old address once, submit `sitemap.xml` in Google Search Console, and watch it for 404s for a few weeks.
