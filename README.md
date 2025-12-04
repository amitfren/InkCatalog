# The Ink Grimoire

A digital, interactive tattoo portfolio designed to look like an ancient, leather-bound book. Features a realistic 3D page-flipping engine and a built-in "Artist Mode" that allows tattoo artists to upload designs, reorder pages, and customize the book's metadata directly from the browser. Includes a "Stand Mode" that generates a QR code for clients to scan and view the portfolio on their own devices.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Version](https://img.shields.io/badge/version-1.1.0-green.svg)

## 🎨 Multi-Artist Support

InkCatalog now supports multiple artist portfolios! Each artist has their own JSON file that defines their portfolio.

### URL Structure

```
index.html                    → Loads default.json (demo portfolio)
index.html?artist=inkgrimoire → Loads inkgrimoire.json
index.html?artist=darkink     → Loads darkink.json
```

### Adding a New Artist

1. Create a new JSON file in the `/portfolios/` folder:
   ```
   portfolios/
     default.json        # Demo/fallback portfolio
     inkgrimoire.json    # Artist 1
     darkink.json        # Artist 2
     your-artist.json    # Add new artists here
   ```

2. Use this template for the JSON file:
   ```json
   {
     "artistHandle": "@yourhandle",
     "bookTitle": "Your Portfolio Title",
     "bookSubtitle": "Your Specialty",
     "coverYear": "Est. 2025",
     "coverStyle": "standard",
     "currentPalette": "classic",
     "backTitle": "Scan to Book",
     "backDescription": "Your description here",
     "transitionEffect": "flip",
     "backCoverQrUrl": "https://instagram.com/yourhandle",
     "pages": [
       { "id": "cover", "type": "cover" },
       {
         "id": "page-1",
         "type": "tattoo",
         "images": ["https://your-image-url.jpg"],
         "layout": "single",
         "caption": "Design Name",
         "description": "Description of the piece"
       },
       { "id": "back-cover", "type": "back-cover" }
     ]
   }
   ```

3. Access the portfolio via: `your-domain.com/?artist=your-artist`

### Portfolio JSON Reference

| Field | Type | Description |
|-------|------|-------------|
| `artistHandle` | string | Instagram/social handle (e.g., "@inkgrimoire") |
| `bookTitle` | string | Main title on the cover |
| `bookSubtitle` | string | Subtitle/specialty |
| `coverYear` | string | Establishment year |
| `coverStyle` | string | "standard" or "custom-image" |
| `currentPalette` | string | Color theme: "classic", "midnight", "forest", "rose" |
| `transitionEffect` | string | Page flip style: "flip", "slide", "fade", "rise" |
| `pages` | array | Array of page objects |

### Page Types

- **Cover**: `{ "id": "cover", "type": "cover" }`
- **Back Cover**: `{ "id": "back-cover", "type": "back-cover" }`
- **Tattoo Page**: See layout options below

### Page Layouts

| Layout | Images | Description |
|--------|--------|-------------|
| `single` | 1 | Full page, single image |
| `double-horizontal` | 2 | Two images side by side |
| `double-vertical` | 2 | Two images stacked |
| `triple-grid` | 3 | Grid layout with 3 images |

---

## Features

### Core Functionality
- **3D Page Flipping Engine**: Realistic book page animations with CSS 3D transforms
- **Responsive Design**: Works seamlessly on desktop, tablet, and mobile devices
- **Single File Architecture**: Everything contained in one HTML file for easy deployment
- **Zero Build Process**: No npm, webpack, or build tools required

### Artist Mode
- **Live Editing**: Customize book title, subtitle, and establishment year
- **Back Cover Customization**: Edit artist handle and description text
- **Image Upload**: Add new tattoo designs directly from your device
- **Page Management**:
  - Reorder pages with up/down controls
  - Edit page captions inline
  - Delete unwanted pages
- **Real-time Preview**: See all changes immediately in the book view

### Stand Mode (QR Code)
- **Client Self-Service**: Generate QR code for clients to scan
- **Mobile Viewing**: Clients can browse the portfolio on their own devices
- **Waiting Room Solution**: Perfect for tattoo shop displays

### Visual Design
- **Vintage Aesthetic**: Parchment textures and aged paper effects
- **Custom Typography**: Cinzel and IM Fell English serif fonts
- **Ambient Lighting**: Wood texture backgrounds and subtle vignettes
- **Corner Ornaments**: Decorative borders on content pages

## Tech Stack

| Technology | Purpose | Source |
|------------|---------|--------|
| **React 18** | UI Framework | CDN (unpkg) |
| **React DOM 18** | DOM Rendering | CDN (unpkg) |
| **Babel Standalone** | In-browser JSX compilation | CDN (unpkg) |
| **Tailwind CSS** | Utility-first styling | CDN |
| **Google Fonts** | Cinzel & IM Fell English | Google Fonts API |
| **Custom SVG Icons** | UI elements (inline, no external deps) | Hand-coded |

## Getting Started

### Installation

No installation required! Simply download the `index.html` file and open it in a modern web browser.

```bash
# Clone or download the file
wget https://your-repo-url/index.html

# Open in browser (macOS)
open index.html

# Open in browser (Linux)
xdg-open index.html

# Open in browser (Windows)
start index.html
```

### Deployment

#### Option 1: Local File
- Open `index.html` directly in any modern browser
- Works offline after initial CDN resource load

#### Option 2: Web Hosting
Deploy to any static hosting service:

**Netlify Drop**
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag and drop `index.html`
3. Done!

**GitHub Pages**
```bash
# In your repo
git add index.html
git commit -m "Add Ink Grimoire portfolio"
git push origin main

# Enable GitHub Pages in repo settings
```

**Vercel**
```bash
vercel deploy
```

#### Option 3: Embedded Display
Perfect for in-shop tablets or kiosks:
```html
<!-- Full-screen kiosk mode -->
<iframe src="index.html" style="width:100vw; height:100vh; border:none;"></iframe>
```

## Usage

### For Artists

#### 1. Enable Artist Mode
Click the gear icon (⚙️) in the top-left corner to open Artist Studio.

#### 2. Customize Book Identity

**Front Cover**
- **Book Title**: Your portfolio name (e.g., "The Ink Grimoire")
- **Subtitle**: Your style specialty (e.g., "Fine Line & Geometric")
- **Est. Year**: Establishment year or portfolio date

**Back Cover**
- **Page Title**: Call to action (e.g., "Scan to Book")
- **Instagram Handle**: Your social media handle
- **Description**: Instructions for clients

#### 3. Manage Content Pages

**Add New Page**
1. Click "Add New Page" in Artist Studio
2. Select an image from your device
3. Page automatically inserted before back cover

**Edit Page Caption**
1. Click the pencil icon (✏️) next to any page
2. Type new caption
3. Click save icon (💾)

**Reorder Pages**
- Click ↑ to move page earlier in book
- Click ↓ to move page later in book
- Cover and back cover cannot be reordered

**Delete Pages**
- Click trash icon (🗑️) next to content pages
- Confirm deletion in popup

#### 4. Generate QR Code for Clients
1. Click "Stand QR" button in top-right
2. QR code displays with current page URL
3. Clients scan to view portfolio on their phones

### For Clients

#### Browsing the Portfolio
- **Click right side** of book to flip page forward
- **Click left side** (on flipped pages) to flip backward
- **Use arrow buttons** at bottom for precise navigation
- **Page counter** shows current position (e.g., "3 / 6")

#### Mobile/Tablet Experience
- Touch corners to flip pages
- Swipe gestures supported
- Responsive layout adapts to screen size

## Customization

### Modifying Initial Content

Edit the `INITIAL_PAGES` array in [index.html:75-110](index.html#L75-L110):

```javascript
const INITIAL_PAGES = [
    { id: 'cover', type: 'cover' },
    {
        id: 'img1',
        type: 'content',
        layout: 'full',
        image: 'your-image-url.jpg',
        caption: 'Your Caption',
        description: 'Your description text.'
    },
    // Add more pages...
    { id: 'back-cover', type: 'back-cover' }
];
```

### Changing Color Scheme

Main color variables in [index.html:14-27](index.html#L14-L27):

```css
/* Background */
background-color: #1a1510; /* Dark brown */

/* Page color */
bg-[#f4e4bc] /* Parchment */

/* Accent colors */
text-amber-950 /* Dark text */
border-amber-900 /* Borders */
```

### Custom Fonts

Replace Google Fonts link in [index.html:12](index.html#L12):

```html
<link href="https://fonts.googleapis.com/css2?family=YourFont&display=swap" rel="stylesheet">
```

Update CSS classes:
```css
.font-cinzel { font-family: 'YourFont', serif; }
```

## Browser Compatibility

| Browser | Minimum Version | Notes |
|---------|----------------|-------|
| Chrome | 90+ | Full support |
| Firefox | 88+ | Full support |
| Safari | 14+ | Full support |
| Edge | 90+ | Full support |
| Mobile Safari | iOS 14+ | Touch optimized |
| Chrome Mobile | 90+ | Touch optimized |

**Requirements:**
- CSS 3D Transforms support
- ES6+ JavaScript support
- `transform-style: preserve-3d` support

## File Structure

```
InkCatalog/
└── index.html          # Single-file application (100% self-contained)
```

All dependencies loaded via CDN:
- React & React DOM
- Babel Standalone (JSX transformation)
- Tailwind CSS
- Google Fonts

**No `node_modules`, no build process, no configuration files needed.**

## Performance Optimization

### Image Optimization Tips
1. **Use WebP format** for smaller file sizes
2. **Compress images** to ~800px width (sufficient for display)
3. **Lazy load** large portfolios (10+ pages)

### CDN Caching
- CDN resources cached by browser after first load
- Works offline once resources are cached
- Consider hosting CDN resources locally for fully offline use

## Troubleshooting

### Icons Not Showing
**Problem**: SVG icons render as empty boxes
**Solution**: Icons are now inline SVGs (no external dependency). If broken, check console for JavaScript errors.

### Images Not Loading
**Problem**: Uploaded images don't display
**Solution**:
- Check browser console for CORS errors
- Ensure image URLs are accessible
- For local images, use `URL.createObjectURL()` (already implemented)

### QR Code Not Working
**Problem**: QR code doesn't scan properly
**Solution**:
- Ensure proper lighting when scanning
- Check that URL is publicly accessible
- Use a dedicated QR scanner app if default camera fails

### Page Flipping Glitches
**Problem**: Pages flip incorrectly or show back-faces
**Solution**:
- Check browser supports `transform-style: preserve-3d`
- Update to latest browser version
- Disable browser hardware acceleration if issues persist

## Privacy & Data

- **No analytics tracking**
- **No external API calls** (except CDN resources and QR code generation)
- **No cookies**
- **No data collection**
- All uploaded images stored in **browser memory only** (lost on page refresh)

### Persistent Storage (Optional)
To save changes between sessions, consider adding:
```javascript
// Save to localStorage
localStorage.setItem('bookData', JSON.stringify(pages));

// Load from localStorage
const savedPages = JSON.parse(localStorage.getItem('bookData'));
```

## License

MIT License - Feel free to use for personal or commercial projects.

## Credits

**Created by**: Amit Frenkel
**Textures**: [Transparent Textures](https://www.transparenttextures.com/)
**Sample Images**: [Unsplash](https://unsplash.com/)
**Fonts**: Google Fonts (Cinzel, IM Fell English)
**QR Code API**: [QR Server](https://goqr.me/api/)

## Roadmap

Future enhancements to consider:
- [ ] Export portfolio as PDF
- [ ] Video support for animated tattoo reveals
- [ ] Multi-language support
- [ ] Cloud storage integration (Firebase/Supabase)
- [ ] Analytics dashboard (page views, scan counts)
- [ ] Print-ready layout export
- [ ] Appointment booking integration
- [ ] Image filters and editing tools
- [ ] Password protection for artist mode
- [ ] Custom domain support

## Support

For issues, questions, or feature requests:
1. Check existing documentation above
2. Review browser console for error messages
3. Verify browser compatibility
4. Open an issue on GitHub (if applicable)

---

**Made with ❤️ for tattoo artists who want a stunning portfolio without the technical headaches.**
