# OpenClaw Consulting Website

Professional consulting website for OpenClaw installation services with payment integration and deployment automation.

**Live Site:** https://neiman-openclaw.dev

## Project Overview

This repository contains a complete consulting website offering OpenClaw installation services, built in ~2 hours with:

- Professional landing page with glassmorphism design
- Stripe payment integration ($49 installation guide)
- SEO optimization (meta tags, structured data, sitemap)
- OpenClaw branding (gradient colors, logo integration)
- Automated GitHub + Cloudflare Pages deployment
- Mobile-responsive design

## Quick Start

### Prerequisites

- GitHub account with personal access token
- Cloudflare account with API token
- Custom domain configured in Cloudflare
- Node.js and npm installed
- wrangler CLI: `npm install -g wrangler`

### Deployment Commands

```bash
# Navigate to repo
cd /path/to/neiman-openclaw-deploy

# Commit changes
git add -A
git commit -m "Your commit message"

# Push to GitHub
git push https://YOUR_TOKEN@github.com/pneiman1/neiman-openclaw.git main

# Deploy to Cloudflare Pages
export CLOUDFLARE_API_TOKEN=YOUR_TOKEN
wrangler pages deploy . --project-name=neiman-openclaw --commit-dirty=true
```

Deployment takes ~1-2 seconds. Changes are live immediately.

## Documentation

- [📚 Complete Deployment Guide](./docs/DEPLOYMENT.md) - Full OpenClaw installation process
- [🌐 Website Building Guide](./docs/WEBSITE_GUIDE.md) - How this site was created
- [⚙️ Services & Integration](./docs/SERVICES.md) - All platforms and tools used
- [🎨 Design System](./docs/DESIGN.md) - Branding, colors, layout
- [🚀 Workflow Reference](./docs/WORKFLOW.md) - Quick deployment commands

## Repository Structure

```
neiman-openclaw-deploy/
├── index.html              # Main website
├── phil.jpg                # Professional headshot
├── openclaw-logo.webp      # OpenClaw logo (80px height)
├── chat-screenshot.jpg     # Demo screenshot
├── robots.txt              # SEO: search engine directives
├── sitemap.xml             # SEO: site structure
├── README.md               # This file
└── docs/                   # Comprehensive documentation
    ├── DEPLOYMENT.md       # OpenClaw deployment guide
    ├── WEBSITE_GUIDE.md    # Website building process
    ├── SERVICES.md         # Platform integrations
    ├── DESIGN.md           # Design system & branding
    └── WORKFLOW.md         # Deployment workflows
```

## Key Features

### 1. Payment Integration
- **Stripe test mode** payment link
- $49 one-time payment for installation guide
- Paywall positioned after prerequisites section
- Easy switch to live mode when ready

### 2. SEO Optimization
- Enhanced meta tags with keywords
- Open Graph tags for social sharing
- Twitter Card tags
- JSON-LD structured data (ProfessionalService schema)
- robots.txt and sitemap.xml
- Fast page load times

### 3. OpenClaw Branding
- Logo gradient: Coral (#C85A4A) → Mauve (#9A8080) → Teal (#6DC8B8)
- Consistent branding throughout site
- "Guaranteed Results" badge with gradient
- Professional tech-focused aesthetic

### 4. Deployment Pipeline
- Git version control
- GitHub repository hosting
- Cloudflare Pages CDN
- Automatic SSL certificates
- Sub-1-minute deploy cycle

### 5. Mobile Responsive
- Optimized for all screen sizes
- Touch-friendly buttons and navigation
- Readable text on small screens
- Compressed images for fast loading

## Credentials & Access

**GitHub Repository:**
- URL: https://github.com/pneiman1/neiman-openclaw
- Token: (stored in deployment scripts)

**Cloudflare:**
- Account ID: aa696abded86f1cbc2de8fcc3800cb60
- API Token: (stored in deployment scripts)
- Project: neiman-openclaw

**Live Site:**
- Production: https://neiman-openclaw.dev
- Preview: https://neiman-openclaw.pages.dev

## Development

### Making Changes

1. Edit `index.html` or other files locally
2. Test changes (optional: local server)
3. Commit and push to GitHub
4. Deploy to Cloudflare Pages
5. Verify changes at neiman-openclaw.dev

### Design Iterations

The site went through ~40 deployment cycles during initial development, refining:
- Guarantee messaging placement
- Section spacing and hierarchy
- Branding consistency
- Mobile responsiveness
- SEO optimization

### Adding New Sections

To add new content:
1. Follow existing section structure in `index.html`
2. Use consistent CSS classes (`.container`, `.section`, etc.)
3. Maintain mobile responsiveness with `@media` queries
4. Update sitemap.xml if adding new pages
5. Test on mobile and desktop

## Next Steps

### Before Going Live
- [ ] Switch Stripe to live mode
- [ ] Set up post-purchase delivery flow (email guide or unlock page)
- [ ] Add Google Analytics for traffic tracking
- [ ] Submit sitemap to Google Search Console
- [ ] Test payment flow end-to-end
- [ ] Add customer testimonials when available

### Marketing
- [ ] Share in OpenClaw Discord community
- [ ] Post to r/selfhosted, r/LocalLLaMA
- [ ] Create Twitter/X thread with screenshots
- [ ] Write blog posts for SEO (link back to site)
- [ ] Create YouTube walkthrough tutorial
- [ ] Submit to directories (clawhub.com, Product Hunt)

### Future Enhancements
- [ ] Blog section for ongoing SEO content
- [ ] Case studies from real installations
- [ ] Video tutorials embedded on site
- [ ] FAQ section with schema markup
- [ ] Live chat integration
- [ ] Customer portal for guide access

## Performance

- **Page Load:** ~1-2 seconds
- **Deploy Time:** ~1-2 seconds
- **Lighthouse Score:** (to be tested)
- **Mobile Score:** (to be tested)

## Tech Stack

- **HTML5** with semantic markup
- **CSS3** with modern features (gradients, glassmorphism, flexbox, grid)
- **No JavaScript framework** (vanilla JS for smooth scroll)
- **SVG graphics** for logos and icons
- **Cloudflare Pages** CDN
- **GitHub** version control
- **Stripe** payment processing

## Support

For questions or issues:
- Email: (add your email)
- Telegram: @pneiman1
- Website: https://neiman.dev

## License

Proprietary - All rights reserved.

## Credits

Built with OpenClaw 🦞 - The self-hosted AI agent framework.

---

Last updated: 2026-02-13
