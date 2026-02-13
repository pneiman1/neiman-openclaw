# Workflow & Commands Reference

Quick reference for common tasks and deployment workflows.

---

## Quick Deploy

```bash
cd /home/ubuntu/.openclaw/workspace/neiman-openclaw-deploy

# Commit changes
git add -A
git commit -m "Your message here"

# Push to GitHub
git push https://YOUR_GITHUB_TOKEN@github.com/pneiman1/neiman-openclaw.git main

# Deploy to Cloudflare
export CLOUDFLARE_API_TOKEN=YOUR_CLOUDFLARE_TOKEN
wrangler pages deploy . --project-name=neiman-openclaw --commit-dirty=true
```

**Time:** 1-2 seconds per deploy

---

## One-Line Deploy

```bash
git add -A && git commit -m "Update" && git push https://TOKEN@github.com/pneiman1/neiman-openclaw.git main && export CLOUDFLARE_API_TOKEN=TOKEN && wrangler pages deploy . --project-name=neiman-openclaw --commit-dirty=true
```

---

## Common Git Commands

```bash
# Status
git status

# View changes
git diff

# View history
git log --oneline

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo changes to file
git checkout -- filename.html

# Create branch
git checkout -b feature-name

# Switch branch
git checkout main

# Merge branch
git merge feature-name
```

---

## Testing Locally

```bash
# Simple Python server
python3 -m http.server 8000

# Visit: http://localhost:8000
```

Or use VS Code Live Server extension.

---

## Image Optimization

```bash
# Install ImageMagick (if needed)
sudo apt install imagemagick

# Compress JPEG
convert input.jpg -quality 85 -strip output.jpg

# Resize image
convert input.jpg -resize 800x600 output.jpg

# Convert to WebP
convert input.jpg -quality 80 output.webp
```

Or use online: https://tinypng.com

---

## SEO Updates

After content changes:

```bash
# Update sitemap lastmod date
sed -i 's/<lastmod>.*<\/lastmod>/<lastmod>2026-02-13<\/lastmod>/' sitemap.xml

# Deploy
git add sitemap.xml
git commit -m "Update sitemap"
# ... deploy commands
```

---

## Stripe Testing

**Test cards:**
```
Success: 4242 4242 4242 4242
Decline: 4000 0000 0000 0002
3D Secure: 4000 0025 0000 3155
```

**Switch to live:**
1. Stripe dashboard → toggle Live mode
2. Update payment link in index.html
3. Deploy

---

## Monitoring

```bash
# Check site status
curl -I https://neiman-openclaw.dev

# Page speed test
curl -o /dev/null -s -w 'Total: %{time_total}s\n' https://neiman-openclaw.dev

# View CloudflarePages analytics
# Visit: Cloudflare dashboard → Pages → neiman-openclaw → Analytics
```

---

## Backup

```bash
# Backup entire repo
tar -czf neiman-openclaw-backup-$(date +%Y%m%d).tar.gz \
    /home/ubuntu/.openclaw/workspace/neiman-openclaw-deploy

# Backup just HTML
cp index.html index-backup-$(date +%Y%m%d).html
```

---

## Emergency Rollback

```bash
# See recent commits
git log --oneline -10

# Rollback to previous commit
git revert HEAD

# Or reset to specific commit
git reset --hard abc1234

# Force push (careful!)
git push -f origin main

# Deploy old version
wrangler pages deploy . --project-name=neiman-openclaw
```

---

## Environment Setup (New Machine)

```bash
# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

# Install wrangler
npm install -g wrangler

# Clone repo
git clone https://github.com/pneiman1/neiman-openclaw.git
cd neiman-openclaw

# Set credentials (add to ~/.bashrc)
export CLOUDFLARE_API_TOKEN=your_token
export GITHUB_TOKEN=your_token

# Deploy
wrangler pages deploy . --project-name=neiman-openclaw
```

---

## Troubleshooting

### Deploy fails

```bash
# Check wrangler version
wrangler --version

# Update wrangler
npm update -g wrangler

# Re-authenticate
wrangler logout
wrangler login
```

### Git push fails

```bash
# Check remote
git remote -v

# Update remote URL
git remote set-url origin https://TOKEN@github.com/pneiman1/neiman-openclaw.git

# Force push (if needed)
git push -f origin main
```

### Site not updating

```bash
# Clear Cloudflare cache
# Visit: Cloudflare dashboard → Caching → Purge Everything

# Or use API
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/purge_cache" \
     -H "Authorization: Bearer TOKEN" \
     -H "Content-Type: application/json" \
     --data '{"purge_everything":true}'
```

---

## Useful Shortcuts

Add to `~/.bashrc`:

```bash
# Quick navigation
alias openclaw='cd /home/ubuntu/.openclaw/workspace/neiman-openclaw-deploy'

# Quick deploy
alias deploy='openclaw && git add -A && git commit -m "Update" && git push origin main && export CLOUDFLARE_API_TOKEN=TOKEN && wrangler pages deploy . --project-name=neiman-openclaw'

# Quick edit
alias edit='openclaw && code index.html'
```

Then: `source ~/.bashrc`

---

## Checklists

### Pre-Deploy Checklist
- [ ] Test locally
- [ ] Check mobile responsiveness
- [ ] Verify all links work
- [ ] Spell check
- [ ] Optimize images
- [ ] Update sitemap date

### Post-Deploy Checklist
- [ ] Visit live site
- [ ] Test payment link
- [ ] Check mobile rendering
- [ ] Verify SSL certificate
- [ ] Test from different locations (VPN)

### Monthly Maintenance
- [ ] Review analytics
- [ ] Update content if needed
- [ ] Check for broken links
- [ ] Review and respond to inquiries
- [ ] Backup repository
- [ ] Update dependencies (wrangler, etc.)

---

Last updated: 2026-02-13
