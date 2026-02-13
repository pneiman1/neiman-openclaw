# Services & Integration Guide

Complete reference for all platforms and services used in this project.

---

## GitHub

**Purpose:** Version control and code hosting

**Account:** pneiman1  
**Repository:** https://github.com/pneiman1/neiman-openclaw

### Setup
1. Create account at https://github.com
2. Create personal access token:
   - Settings → Developer settings → Personal access tokens → Tokens (classic)
   - Generate new token
   - Scopes: `repo`, `workflow`
3. Save token securely

### Commands
```bash
git push https://TOKEN@github.com/pneiman1/neiman-openclaw.git main
```

---

## Cloudflare Pages

**Purpose:** CDN, hosting, SSL, deployment

**Account ID:** aa696abded86f1cbc2de8fcc3800cb60  
**Project:** neiman-openclaw

### Setup
1. Sign up at https://pages.cloudflare.com
2. Get API token:
   - My Profile → API Tokens → Create Token
   - Use "Edit Cloudflare Workers" template
   - Account: Include specific account → Select your account
   - Zone: All zones
   - Save token

### Deployment
```bash
export CLOUDFLARE_API_TOKEN=your_token
wrangler pages deploy . --project-name=neiman-openclaw --commit-dirty=true
```

---

## Stripe

**Purpose:** Payment processing

**Product:** OpenClaw Installation Guide  
**Price:** $49 one-time

### Setup
1. Create account at https://stripe.com
2. Create product:
   - Dashboard → Products → Add product
   - Name, price, description
3. Create payment link:
   - Products → Your product → Create payment link
4. Test mode vs Live mode:
   - Toggle in dashboard (top right)
   - Use test card: 4242 4242 4242 4242

---

## Domain Configuration

**Domain:** neiman-openclaw.dev

### DNS Setup (Cloudflare)
1. Add domain to Cloudflare
2. Update nameservers at registrar
3. Connect to Pages:
   - Pages → Custom domains → Add
   - Auto-configures DNS
4. SSL: Automatic (Cloudflare)

---

## Credentials Management

**Never commit:**
- API tokens
- Stripe keys
- GitHub tokens

**Store in:**
- Environment variables
- Password manager (1Password, Bitwarden)
- Encrypted notes

**For deployment scripts:**
```bash
export CLOUDFLARE_API_TOKEN=xxx
export GITHUB_TOKEN=xxx
```

---

Last updated: 2026-02-13
