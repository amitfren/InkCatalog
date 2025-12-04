# InkCatalog Architecture Options

## Overview

This document outlines the architecture options for scaling InkCatalog from a single-artist portfolio to a multi-artist platform.

---

## 🏗️ Architecture Options

### Option 1: Static Hosting + JSON Files (Simple Start)

**Best for:** 1-10 artists, minimal backend, quick launch

```
/public/
  index.html              # Main app
  /portfolios/
    inkgrimoire.json      # Artist 1 portfolio data
    darkink_studio.json   # Artist 2 portfolio data
    shadowtattoo.json     # Artist 3 portfolio data
```

**How it works:**
- Single HTML app loads portfolio based on URL parameter
- Portfolio data stored as JSON files
- No backend required
- Artists send you their portfolio JSON, you upload manually

**URL Structure:**
```
inkcatalog.com/?artist=inkgrimoire
inkcatalog.com/?artist=darkink_studio
```

**Pros:**
- ✅ Zero hosting costs (Cloudflare Pages free tier)
- ✅ No backend to maintain
- ✅ Fast loading (CDN cached)
- ✅ Simple deployment

**Cons:**
- ❌ Manual portfolio management
- ❌ No self-service for artists
- ❌ Image hosting separate concern

---

### Option 2: Single App + Cloud Storage (Better)

**Best for:** 10-100 artists, some automation needed

```
┌─────────────────────────────────────────────────────┐
│                    Frontend                          │
│              (Cloudflare Pages)                      │
│                                                      │
│  index.html - Portfolio Viewer & Artist Studio       │
└─────────────────────┬───────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────┐
│                 Cloud Storage                        │
│           (Cloudflare R2 / AWS S3)                  │
│                                                      │
│  /portfolios/                                        │
│    inkgrimoire.json                                  │
│    darkink_studio.json                               │
│  /images/                                            │
│    /inkgrimoire/                                     │
│      tattoo1.jpg                                     │
│      tattoo2.jpg                                     │
│    /darkink_studio/                                  │
│      design1.jpg                                     │
└─────────────────────────────────────────────────────┘
```

**URL Structure Options:**

```
# Query Parameter
inkcatalog.com/?artist=inkgrimoire

# Path-based (requires routing)
inkcatalog.com/p/inkgrimoire
inkcatalog.com/portfolio/darkink_studio

# Subdomain (requires DNS setup)
inkgrimoire.inkcatalog.com
darkink.inkcatalog.com
```

**Pros:**
- ✅ Scalable storage
- ✅ Still no backend server
- ✅ CDN-cached images
- ✅ Low cost ($0-5/month)

**Cons:**
- ❌ Still manual upload process
- ❌ No artist authentication

---

### Option 3: Full Platform with Auth (Scalable)

**Best for:** 100+ artists, self-service, monetization

```
┌─────────────────────────────────────────────────────────────────┐
│                         Frontend                                 │
│                    (Vercel / Cloudflare)                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │  Portfolio  │  │   Artist    │  │    Admin Dashboard      │  │
│  │   Viewer    │  │   Studio    │  │   (optional)            │  │
│  │  (public)   │  │  (auth req) │  │                         │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
│                                                                  │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Backend Services                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │    Auth     │  │  Database   │  │    File Storage         │  │
│  │  (Supabase  │  │  (Supabase  │  │   (Cloudflare R2 /     │  │
│  │   / Clerk)  │  │  /Firebase) │  │    Supabase Storage)    │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │  Analytics  │  │  Payments   │  │    Email Service        │  │
│  │ (Plausible) │  │  (Stripe)   │  │   (Resend / Postmark)   │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Database Schema:**

```sql
-- Users/Artists
CREATE TABLE artists (
  id UUID PRIMARY KEY,
  email VARCHAR UNIQUE NOT NULL,
  handle VARCHAR UNIQUE NOT NULL,
  display_name VARCHAR,
  created_at TIMESTAMP DEFAULT NOW(),
  plan VARCHAR DEFAULT 'free'  -- free, pro, premium
);

-- Portfolios
CREATE TABLE portfolios (
  id UUID PRIMARY KEY,
  artist_id UUID REFERENCES artists(id),
  title VARCHAR NOT NULL,
  subtitle VARCHAR,
  settings JSONB,  -- colors, fonts, transitions
  is_published BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Portfolio Pages
CREATE TABLE pages (
  id UUID PRIMARY KEY,
  portfolio_id UUID REFERENCES portfolios(id),
  type VARCHAR NOT NULL,  -- cover, back-cover, tattoo
  position INTEGER NOT NULL,
  content JSONB,  -- images, captions, layout
  created_at TIMESTAMP DEFAULT NOW()
);

-- Analytics (optional)
CREATE TABLE page_views (
  id UUID PRIMARY KEY,
  portfolio_id UUID REFERENCES portfolios(id),
  viewed_at TIMESTAMP DEFAULT NOW(),
  referrer VARCHAR,
  user_agent VARCHAR
);
```

**Pros:**
- ✅ Full self-service for artists
- ✅ Scalable to thousands of users
- ✅ Monetization ready
- ✅ Analytics and insights
- ✅ Custom domains per artist

**Cons:**
- ❌ More complex to build
- ❌ Ongoing maintenance
- ❌ Higher costs at scale

---

## 🔐 Authentication Options

### Do You Need Authentication?

| Scenario | Auth Needed? | Reason |
|----------|--------------|--------|
| Single artist, personal use | ❌ No | Just you |
| Multiple artists, you manage all | ❌ No | You control uploads |
| Artists self-manage portfolios | ✅ Yes | Protect their data |
| Public portfolio viewing | ❌ No | Viewers don't need accounts |
| Premium features / payments | ✅ Yes | Need user accounts |

### Recommended Auth Providers

| Provider | Free Tier | Best For | Notes |
|----------|-----------|----------|-------|
| **Supabase Auth** | 50,000 MAU | Full-stack apps | Includes database |
| **Clerk** | 10,000 MAU | Beautiful UI | Easy setup, great DX |
| **Auth0** | 7,000 MAU | Enterprise | Feature-rich |
| **Firebase Auth** | Unlimited | Google ecosystem | Well documented |
| **Lucia** | Self-hosted | Full control | Open source |

### Simple Auth Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Landing   │ ──▶ │    Login    │ ──▶ │   Artist    │
│    Page     │     │   (email/   │     │   Studio    │
│             │     │   social)   │     │             │
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Portfolio  │
                    │   Editor    │
                    └─────────────┘
```

---

## 🔗 URL & Short Link Strategy

### Option A: Native Long URLs with QR

QR codes can encode any URL length - no shortener needed:

```
https://inkcatalog.com/portfolio/inkgrimoire
https://inkcatalog.com/p/darkink_studio
```

**QR Code Generation:**
```
https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=YOUR_URL
```

### Option B: Custom Short Domain

Purchase a short domain for marketing:

| Domain | Cost/Year | Example |
|--------|-----------|---------|
| ink.cat | ~$30 | ink.cat/grimoire |
| tat.ink | ~$35 | tat.ink/shadow |
| tatt.co | ~$25 | tatt.co/artist |

**Implementation with Cloudflare Workers:**

```javascript
// Cloudflare Worker for redirects
export default {
  async fetch(request) {
    const url = new URL(request.url);
    const slug = url.pathname.slice(1); // Remove leading /
    
    // Redirect to full portfolio URL
    return Response.redirect(
      `https://inkcatalog.com/portfolio/${slug}`,
      301
    );
  }
}
```

### Option C: Dynamic Short Links

Generate unique short links per portfolio:

```
ink.cat/a1b2c3 → inkcatalog.com/portfolio/inkgrimoire
ink.cat/x9y8z7 → inkcatalog.com/portfolio/darkink
```

**Requires:** Database to store mappings

---

## 🌐 Hosting Recommendations

### Comparison Table

| Provider | Free Tier | Bandwidth | Best For | Deploy From |
|----------|-----------|-----------|----------|-------------|
| **Cloudflare Pages** | ✅ Yes | Unlimited | Static sites | GitHub/GitLab |
| **Vercel** | ✅ Yes | 100GB/mo | React/Next.js | GitHub/GitLab |
| **Netlify** | ✅ Yes | 100GB/mo | Static + Functions | GitHub/GitLab |
| **GitHub Pages** | ✅ Yes | 100GB/mo | Simple static | GitHub only |
| **Render** | ✅ Yes | 100GB/mo | Full-stack | GitHub/GitLab |
| **Railway** | $5 credit | Pay as you go | Backends | GitHub |

### Storage Options

| Provider | Free Tier | Cost After | Best For |
|----------|-----------|------------|----------|
| **Cloudflare R2** | 10GB | $0.015/GB | Images, JSON |
| **Supabase Storage** | 1GB | $0.021/GB | Integrated with auth |
| **AWS S3** | 5GB (12mo) | $0.023/GB | Enterprise |
| **Backblaze B2** | 10GB | $0.005/GB | Budget option |

### Recommended Stack by Scale

**Solo Artist (Free):**
```
Hosting: GitHub Pages or Cloudflare Pages
Storage: Images in repo (< 100MB) or Imgur
Database: None (JSON file)
Cost: $0/month
```

**Small Studio (1-10 artists, ~$5/month):**
```
Hosting: Cloudflare Pages
Storage: Cloudflare R2
Database: JSON files or Supabase free tier
Cost: $0-5/month
```

**Growing Platform (10-100 artists, ~$20/month):**
```
Hosting: Vercel
Storage: Cloudflare R2 or Supabase Storage
Database: Supabase (PostgreSQL)
Auth: Supabase Auth or Clerk
Cost: $10-25/month
```

**Scale Platform (100+ artists, ~$50+/month):**
```
Hosting: Vercel Pro or AWS
Storage: Cloudflare R2 + CDN
Database: Supabase Pro or PlanetScale
Auth: Clerk or Auth0
Payments: Stripe
Cost: $50-200/month
```

---

## 📱 Mobile Considerations

### Progressive Web App (PWA)

Add PWA capabilities for mobile users:

```json
// manifest.json
{
  "name": "InkCatalog",
  "short_name": "InkCatalog",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#1a1510",
  "theme_color": "#f59e0b",
  "icons": [
    {
      "src": "/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

### Offline Support

Service worker for offline viewing:

```javascript
// sw.js
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open('inkcatalog-v1').then((cache) => {
      return cache.addAll([
        '/',
        '/index.html',
        '/portfolios/default.json'
      ]);
    })
  );
});
```

---

## 💰 Monetization Options

### Freemium Model

| Tier | Price | Features |
|------|-------|----------|
| **Free** | $0 | 1 portfolio, 10 pages, InkCatalog branding |
| **Pro** | $9/mo | 3 portfolios, 50 pages, custom domain, no branding |
| **Studio** | $29/mo | Unlimited portfolios, analytics, priority support |

### One-Time Purchase

| Option | Price | Features |
|--------|-------|----------|
| **Lifetime** | $49 | Pro features forever |
| **White Label** | $199 | Remove all InkCatalog branding |

### Commission Model

- Free to use
- 5% commission on bookings made through QR code links
- Requires booking integration (Calendly, Square, etc.)

---

## 🚀 Implementation Roadmap

### Phase 1: MVP (Week 1-2)
- [ ] Add URL parameter loading (`?artist=handle`)
- [ ] Create `/portfolios/` folder structure
- [ ] Deploy to Cloudflare Pages
- [ ] Set up basic analytics (Plausible or Umami)
- [ ] Create 2-3 demo portfolios

### Phase 2: Multi-Artist (Week 3-4)
- [ ] Set up Cloudflare R2 for image storage
- [ ] Create portfolio upload/import tool
- [ ] Add subdomain support
- [ ] Implement short URL redirects

### Phase 3: Self-Service (Week 5-8)
- [ ] Add Supabase auth
- [ ] Create artist registration flow
- [ ] Build portfolio management API
- [ ] Add image upload functionality
- [ ] Implement save to cloud

### Phase 4: Platform (Month 2-3)
- [ ] Add Stripe payments
- [ ] Build admin dashboard
- [ ] Implement analytics per portfolio
- [ ] Add custom domain support
- [ ] Create onboarding flow

---

## 📁 Recommended File Structure

### Simple (Static)
```
/InkCatalog/
├── index.html
├── portfolios/
│   ├── default.json
│   ├── inkgrimoire.json
│   └── darkink.json
├── images/
│   ├── inkgrimoire/
│   │   ├── tattoo1.jpg
│   │   └── tattoo2.jpg
│   └── darkink/
│       └── design1.jpg
├── README.md
├── ARCHITECTURE.md
└── MOBILE_SETUP.md
```

### Full Platform
```
/InkCatalog/
├── apps/
│   ├── web/                 # Main portfolio viewer
│   │   ├── src/
│   │   ├── public/
│   │   └── package.json
│   └── studio/              # Artist dashboard
│       ├── src/
│       └── package.json
├── packages/
│   ├── ui/                  # Shared components
│   ├── database/            # DB schema & queries
│   └── config/              # Shared config
├── supabase/
│   ├── migrations/
│   └── functions/
├── docs/
│   ├── ARCHITECTURE.md
│   └── API.md
└── package.json
```

---

## 🔒 Security Considerations

### Data Protection
- [ ] HTTPS everywhere (automatic with Cloudflare/Vercel)
- [ ] Sanitize user inputs
- [ ] Rate limiting on API endpoints
- [ ] Image upload validation (type, size)

### Privacy
- [ ] GDPR compliance for EU users
- [ ] Clear privacy policy
- [ ] Data export/delete functionality
- [ ] Cookie consent for analytics

### Access Control
- [ ] Artists can only edit own portfolios
- [ ] Public portfolios are read-only
- [ ] Admin role for platform management

---

## 📞 Next Steps

1. **Decide on initial scale** - How many artists in first 3 months?
2. **Choose hosting** - Cloudflare Pages recommended for start
3. **Implement URL loading** - Enable multi-artist with query params
4. **Set up storage** - Cloudflare R2 for images
5. **Add analytics** - Track portfolio views
6. **Plan auth** - If self-service needed

---

*Last updated: November 2025*
