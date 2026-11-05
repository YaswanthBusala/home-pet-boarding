# Home Pet Boarding Center & Day Care Services – Website

A fast, mobile-first Angular website for a home-based dog boarding and day care business in Boduppal, Hyderabad.
Enquiries go straight to **WhatsApp** and **phone calls**. No backend, database or API keys are needed.

- Angular 22 (standalone components, signals, zoneless), TypeScript, SCSS
- Reactive Forms booking enquiry → pre-filled WhatsApp message
- Gallery with filters, a fullscreen lightbox (keyboard, swipe), and lazy-loaded images
- Local SEO: title/meta, Open Graph, Schema.org `LocalBusiness` JSON-LD, `sitemap.xml`, `robots.txt`
- WCAG 2.2 AA-minded: skip link, semantic landmarks, labelled controls, focus management, reduced-motion support
- Azure Static Web Apps ready, with security headers (CSP, HSTS, etc.)

---

## 1. Requirements

| Tool    | Version                                    |
| ------- | ------------------------------------------ |
| Node.js | **22.22.3+** or **24.15+** (LTS recommended) |
| npm     | 10+ (comes with Node)                      |

Install Node.js from <https://nodejs.org>. The Angular CLI is included as a dev dependency, so a global install is optional:

```bash
npm install -g @angular/cli   # optional
```

> **Windows PowerShell tip:** if you see "running scripts is disabled", use `npm.cmd` instead of `npm`
> (e.g. `npm.cmd install`) or run the commands in Command Prompt.

## 2. Run locally

```bash
npm install        # install dependencies
npm start          # dev server at http://localhost:4200
```

## 3. Production build

```bash
npm run build
```

Output folder: **`dist/home-pet-boarding/browser`**. This is the folder you deploy.

---

## 4. Update business information

Everything lives in **`src/app/config/`**. Components read only from these files.

| File                      | What to change                                                                                               |
| ------------------------- | ------------------------------------------------------------------------------------------------------------ |
| `business.config.ts`      | Name, phone, WhatsApp number, address, Google Maps link, pets (Rio & Coco), default WhatsApp message          |
| `gallery.config.ts`       | Gallery images, categories, captions and alt text                                                            |
| `testimonials.config.ts`  | Genuine customer reviews (leave empty to show the "share your experience" invitation)                         |

**Optional channels are hidden until configured:**

- `email: 'hello@yourdomain.in'` → shows the Email card and footer link (uses `mailto:`)
- `instagramUrl: 'https://www.instagram.com/yourprofile'` → shows the Instagram card and footer link
- `siteUrl: 'https://www.yourdomain.in'` → adds the canonical URL, `og:url`, and `url`/`image` in the structured data

After changing `siteUrl`, also update:

- `public/sitemap.xml`: replace `https://YOUR-DOMAIN/`
- `public/robots.txt`: uncomment the `Sitemap:` line and set your domain
- `src/index.html`: the static title, description and Open Graph tags, if you change the wording (they're there for crawlers that don't run JavaScript)

## 5. Replace the sample images

All images are lightweight **sample illustrations** (SVG), clearly marked "Sample". Replace them with real photographs when ready:

1. Copy your photos into `public/images/` (Rio & Coco, hero) or `public/images/gallery/`.
   Recommended: JPG or WebP, about 1600px on the longest side, under 300 KB each (compress at <https://squoosh.app>).
2. **Rio & Coco:** in `business.config.ts`, set `image: 'images/rio.jpg'`, give an honest `imageAlt` (e.g. "Rio, our Shih Tzu, on the sofa") and set `imageIsSample: false`.
3. **Gallery:** in `gallery.config.ts`, update `src`, `alt`, `caption`, `width`/`height` (the photo's pixel size) and set `isSample: false`.
4. **Hero:** replace `public/images/hero.svg` (or update the `src` in `src/app/components/hero/hero.component.html` and the preload link in `src/index.html`), then remove the "Sample image" badge in that template.
5. When no sample images remain, remove the sample-image note at the bottom of `footer.component.html` and in the gallery intro text.
6. Rebuild and redeploy. With GitHub Actions, just push to `main`.

> Only label photos as Rio, Coco, your home or customer pets if they genuinely are, and get permission from pet parents before sharing photos of their dogs.

---

## 6. Deploy to Azure Static Web Apps

### Option A – GitHub Actions (recommended)

1. Push this project to a GitHub repository.
2. In the [Azure Portal](https://portal.azure.com), go to **Create a resource → Static Web App**.
   - Plan: **Free**
   - Source: **GitHub**, then pick your repo and the `main` branch
   - Build preset: **Angular**
   - App location: `/`
   - Output location: `dist/home-pet-boarding/browser`
3. Azure adds a workflow and the `AZURE_STATIC_WEB_APPS_API_TOKEN_*` secret to your repo.
   You can use the workflow already included in this project (`.github/workflows/azure-static-web-apps.yml`):
   delete the auto-generated one, then rename the secret to `AZURE_STATIC_WEB_APPS_API_TOKEN`, or edit the secret name in the workflow.
4. Every push to `main` builds and deploys automatically. Pull requests get preview URLs.

### Option B – Manual deploy with the SWA CLI

```bash
npm run build
npx @azure/static-web-apps-cli deploy ./dist/home-pet-boarding/browser --env production --deployment-token <YOUR_TOKEN>
```

Get the deployment token from **Static Web App → Overview → Manage deployment token**. Never commit it to the repo.

### Hosting configuration

`public/staticwebapp.config.json` is copied into the build output. It sets:

- SPA navigation fallback to `index.html` (unknown URLs show the home page)
- Security headers: Content-Security-Policy, HSTS, X-Content-Type-Options, X-Frame-Options, Referrer-Policy, Permissions-Policy
- Caching: hashed JS/CSS cached for 1 year, images for 7 days, `index.html` always revalidated

If you later add third-party scripts or embeds (analytics, Google Maps iframe, etc.), add their domains to the CSP.

## 7. Custom domain

1. Azure Portal → your Static Web App → **Custom domains → + Add**.
2. **For `www.yourdomain.in`:** at your domain registrar, add a **CNAME** record pointing `www` to your `*.azurestaticapps.net` hostname.
3. **For the root domain `yourdomain.in`:** add the **TXT** validation record Azure shows, then an **ALIAS/ANAME** record (or an A record if your DNS provider doesn't support ALIAS).
4. Wait for validation (minutes to a few hours).
5. Set `siteUrl` in `business.config.ts`, update `sitemap.xml` and `robots.txt`, then redeploy.
6. Submit the sitemap in [Google Search Console](https://search.google.com/search-console) and add the website URL to your Google Business Profile.

## 8. HTTPS

- Azure Static Web Apps gives **free, auto-renewing TLS certificates** for both the default `*.azurestaticapps.net` domain and custom domains. No setup is needed.
- HTTP requests are redirected to HTTPS automatically.
- The `Strict-Transport-Security` header tells browsers to always use HTTPS.

---

## Project structure

```
public/                    static files copied as-is to the site root
  images/                  hero, Rio & Coco, logo (sample illustrations)
    gallery/               gallery images (sample illustrations)
  robots.txt, sitemap.xml, staticwebapp.config.json, favicon.*
src/
  app/
    components/            navbar, hero, about, services, gallery, why-choose-us,
                           how-it-works, testimonials, booking, contact, footer, whatsapp-float
    config/                business.config.ts, gallery.config.ts, testimonials.config.ts
    pages/home/            single-page layout composing all sections
    shared/                icon component, contact-link helpers, SEO service, scroll-reveal directive
    app.component.ts, app.config.ts, app.routes.ts
  index.html, main.ts, styles.scss
```

## Privacy & security notes

- The booking form doesn't store anything (no cookies, localStorage or backend). Details are only sent when the visitor presses send in WhatsApp.
- All user input is length-limited, validated and URL-encoded with `encodeURIComponent`.
- External links use `rel="noopener noreferrer"`. There are no secrets or API keys in the code.

## Credits

Icons are Lucide-style SVG paths (ISC licence) and the WhatsApp glyph, both embedded inline. Fonts are Fraunces and Nunito (Google Fonts, SIL OFL).
