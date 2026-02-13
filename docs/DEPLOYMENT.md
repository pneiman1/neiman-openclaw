# Complete OpenClaw Deployment Guide

**Last Updated:** 2026-02-13  
**Estimated Time:** 30-45 minutes (first time), 15-20 minutes (if AWS ready)

This is a battle-tested guide to deploying OpenClaw on AWS EC2 with Telegram integration. Written during real installations with every gotcha documented.

---

## Table of Contents

1. [Overview & Architecture](#overview--architecture)
2. [Prerequisites](#prerequisites)
3. [Step 1: Provision AWS EC2 Instance](#step-1-provision-aws-ec2-instance)
4. [Step 2: Configure Security Groups](#step-2-configure-security-groups)
5. [Step 3: Connect via SSH](#step-3-connect-via-ssh)
6. [Step 4: Install Node.js & OpenClaw](#step-4-install-nodejs--openclaw)
7. [Step 5: Create Telegram Bot](#step-5-create-telegram-bot)
8. [Step 6: Run Onboarding Wizard](#step-6-run-onboarding-wizard)
9. [Step 7: Pair Telegram Account](#step-7-pair-telegram-account)
10. [Step 8: Run as Persistent Service](#step-8-run-as-persistent-service)
11. [Troubleshooting Guide](#troubleshooting-guide)
12. [Cost Breakdown](#cost-breakdown)
13. [Production Hardening](#production-hardening)

---

## Overview & Architecture

### What is OpenClaw?

OpenClaw is an open-source, self-hosted AI agent that connects to messaging platforms (Telegram, WhatsApp, Discord, Slack). It acts as a gateway between you and AI models like Claude or GPT, letting you interact with a powerful AI assistant directly from your phone.

### Architecture

```
Telegram (your phone)
↓
OpenClaw Gateway (EC2 instance, always running)
↓
AI Model API (Anthropic Claude, OpenAI GPT, etc.)
↓
Response back to Telegram
```

### Why Self-Host?

- **Privacy:** Your data never leaves your control
- **Cost:** $0.05 per conversation vs. $20-200/month for ChatGPT Plus
- **Customization:** Full control over behavior, tools, integrations
- **Always Available:** 24/7 access from any device with Telegram

---

## Prerequisites

### Required

1. **AWS Account**
   - Free tier eligible works for initial setup
   - Credit card required (even for free tier)
   - Sign up at: https://aws.amazon.com

2. **Telegram Account**
   - Install Telegram on your phone
   - Create account if needed
   - You'll message @BotFather to create your bot

3. **AI API Key**
   - **Recommended:** Anthropic Claude (https://console.anthropic.com)
   - Alternative: OpenAI (https://platform.openai.com)
   - Free tier available for testing

4. **Terminal Application**
   - **Mac/Linux:** Terminal (built-in)
   - **Windows:** PowerShell or Windows Terminal

5. **Basic Command Line Comfort**
   - Ability to copy-paste commands
   - No coding experience required
   - We'll walk through everything

### Recommended

- SSH key management tool (optional)
- Text editor for notes
- Backup of your .pem key file

---

## Step 1: Provision AWS EC2 Instance

### Critical: Choose the Right Instance Size

⚠️ **DO NOT USE t3.micro** - Only 1GB RAM, OpenClaw WILL crash.

✅ **Use t3.small minimum** - 2GB RAM, runs smoothly.

### Launch Instance

1. Log in to AWS Console: https://console.aws.amazon.com
2. Search for "EC2" in the services search bar
3. Click **Launch Instance**

### Configuration Settings

| Setting | Value | Notes |
|---------|-------|-------|
| **Name** | `openclaw-bot` | Or any name you prefer |
| **AMI** | Ubuntu 24.04 LTS | Latest LTS version |
| **Instance type** | **t3.small** | 2GB RAM minimum |
| **Key pair** | Create new | Download .pem file |
| **Storage** | 8 GB gp3 | Default is fine |
| **Network** | Default VPC | No changes needed |

### Creating Key Pair

1. Click "Create new key pair"
2. Name: `openclaw-key` (or your preference)
3. Type: RSA
4. Format: .pem (Mac/Linux) or .ppk (Windows PuTTY)
5. Click **Create key pair**
6. **SAVE THIS FILE SECURELY** - It's your only way to access the server

⚠️ **Key Pair Warning:** If you lose this .pem file, you're permanently locked out of your instance. Back it up somewhere safe.

### Launch

Click **Launch Instance** at the bottom right.

Wait 1-2 minutes for the instance to start. Status will show "Running" when ready.

---

## Step 2: Configure Security Groups

Security groups are AWS's firewall. You need to open specific ports.

### Find Security Group

1. Click on your instance in EC2 dashboard
2. Scroll to **Security** tab
3. Click on the security group name (sg-xxxxxxxxx)

### Add Inbound Rules

Click **Edit inbound rules** → **Add rule**

| Port | Protocol | Source | Description |
|------|----------|--------|-------------|
| 22 | TCP | Your IP /32 | SSH access |
| 18789 | TCP | Your IP /32 | OpenClaw Dashboard (optional) |

### Finding Your IP Address

1. Google "what is my IP"
2. Copy the IP address (e.g., 98.45.123.67)
3. Add `/32` to the end: `98.45.123.67/32`

**What `/32` means:** Only this one IP can connect. More secure than `0.0.0.0/0` (anyone).

### Save Rules

Click **Save rules** at the bottom right.

---

## Step 3: Connect via SSH

### Get Your Public IP

1. EC2 Dashboard → Instances
2. Click your instance
3. Copy the **Public IPv4 address** (e.g., 3.145.67.89)

### Fix Key Permissions (Required)

**Mac/Linux:**
```bash
chmod 400 ~/Downloads/openclaw-key.pem
```

**Windows PowerShell:**
```powershell
icacls "C:\Users\YourName\Downloads\openclaw-key.pem" /inheritance:r /grant:r "%USERNAME%:R"
```

### Connect

**Mac/Linux:**
```bash
ssh -i ~/Downloads/openclaw-key.pem ubuntu@YOUR_PUBLIC_IP
```

**Windows PowerShell:**
```powershell
ssh -i C:\Users\YourName\Downloads\openclaw-key.pem ubuntu@YOUR_PUBLIC_IP
```

### First Connection

You'll see:
```
The authenticity of host 'X.X.X.X' can't be established.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

⚠️ **Type the full word:** `yes` (not just Enter)

Press Enter. You're now connected to your Ubuntu server!

---

## Step 4: Install Node.js & OpenClaw

### Update System

```bash
sudo apt update && sudo apt upgrade -y
```

This takes 1-2 minutes. Safe to run even on first boot.

### Install Node.js 22

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

Verify installation:
```bash
node --version  # Should show v22.x.x
npm --version   # Should show 10.x.x
```

### Configure npm Global Directory

This avoids permission issues:

```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### Install OpenClaw

```bash
npm install -g openclaw
```

Takes 1-2 minutes. Verify:

```bash
openclaw --version
```

Should show current version (e.g., 0.x.x).

---

## Step 5: Create Telegram Bot

### Message @BotFather

1. Open Telegram on your phone
2. Search for `@BotFather` (official Telegram bot)
3. Start a chat

### Create New Bot

Send this command:
```
/newbot
```

BotFather will ask questions:

**1. Bot name** (display name):
```
My OpenClaw Assistant
```
(Or any name you want)

**2. Bot username** (must be unique, must end in "bot"):
```
myclaw_assistant_bot
```

If taken, try: `your_name_openclaw_bot`

### Save Your Token

BotFather will reply with:
```
Done! Your bot token is:
1234567890:ABCdefGHIjklMNOpqrsTUVwxyz
```

**Copy this token!** You'll need it in the next step.

🔑 **Security:** Never share this token publicly. It's the password to your bot.

### Customize Bot (Optional)

Send these commands to BotFather:

```
/setdescription @your_bot_name
```
Add a description like: "My personal AI assistant powered by OpenClaw"

```
/setabouttext @your_bot_name
```
Add about text: "24/7 AI assistant with Claude"

```
/setuserpic @your_bot_name
```
Upload a profile picture

---

## Step 6: Run Onboarding Wizard

### Start Wizard

```bash
openclaw onboard
```

This interactive wizard configures OpenClaw.

### Answer Prompts

| Prompt | What to Enter | Notes |
|--------|---------------|-------|
| **Telegram bot token** | Paste token from BotFather | Press Enter after pasting |
| **Package manager** | Select `npm` | Use arrow keys, Enter to select |
| **Install skills?** | `Skip for now` | Can add later |
| **API Keys** | Enter Anthropic API key | From console.anthropic.com |
| **AI Model** | Select `claude-sonnet-4-5` | Recommended for quality |
| **Hatch** | Select "Hatch in TUI" | Starts the gateway |

### Getting Anthropic API Key

1. Go to: https://console.anthropic.com
2. Sign up / Sign in
3. Click **Get API Keys**
4. Click **Create Key**
5. Name it "OpenClaw"
6. Copy the key (starts with `sk-ant-`)

💡 **Free Tier:** Anthropic gives $5 free credit for testing.

### During Onboarding

You'll see output like:
```
✓ Telegram connected
✓ Configuration saved
✓ Starting gateway...
```

**If you see "JavaScript heap out of memory":** See [Troubleshooting](#troubleshooting-guide).

---

## Step 7: Pair Telegram Account

### Send First Message

1. Open Telegram
2. Find your bot (search for the username you created)
3. Click **Start**
4. Send any message (e.g., "hello")

### Bot Responds with Code

Your bot will reply:
```
🔐 Pairing required!
Code: abc123xyz
```

### Approve Pairing

Back in your SSH terminal (where OpenClaw is running):

```bash
openclaw pairing approve telegram abc123xyz
```

Replace `abc123xyz` with the actual code from your bot.

You'll see:
```
✓ Pairing approved
✓ Session linked
```

### Test It!

Message your bot again:
```
What's the weather?
```

Your bot should respond! 🎉

---

## Step 8: Run as Persistent Service

Right now, OpenClaw stops if you close SSH. Let's make it run 24/7.

### Stop Current Gateway

Press `Ctrl+C` in the terminal to stop OpenClaw.

### Create systemd Service

```bash
sudo tee /etc/systemd/system/openclaw-gateway.service << 'EOF'
[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu
ExecStart=/home/ubuntu/.npm-global/bin/openclaw gateway start --foreground
Restart=always
RestartSec=10
Environment=PATH=/home/ubuntu/.npm-global/bin:/usr/local/bin:/usr/bin:/bin

[Install]
WantedBy=multi-user.target
EOF
```

### Enable and Start Service

```bash
sudo systemctl daemon-reload
sudo systemctl enable openclaw-gateway
sudo systemctl start openclaw-gateway
```

### Verify It's Running

```bash
sudo systemctl status openclaw-gateway
```

Should show:
```
● openclaw-gateway.service - OpenClaw Gateway
   Active: active (running) since...
```

Press `q` to exit.

### Useful Commands

| Command | Purpose |
|---------|---------|
| `sudo systemctl status openclaw-gateway` | Check if running |
| `sudo systemctl restart openclaw-gateway` | Restart after config changes |
| `sudo systemctl stop openclaw-gateway` | Stop the service |
| `sudo journalctl -u openclaw-gateway -f` | View live logs |
| `sudo journalctl -u openclaw-gateway --since "1 hour ago"` | Recent logs |

### Test Persistence

1. Close your SSH connection
2. Wait 10 seconds
3. Message your Telegram bot
4. It should respond!

✅ **Success!** Your bot is now running 24/7.

---

## Troubleshooting Guide

### 11.1 JavaScript Heap Out of Memory

**Symptom:**
```
FATAL ERROR: Reached heap limit Allocation failed - JavaScript heap out of memory
```

**Cause:** t3.micro only has 1GB RAM. OpenClaw needs 2GB minimum.

**Fix A: Resize Instance (Recommended)**

1. **Stop instance:**
   - EC2 Dashboard → Instances
   - Select your instance
   - Actions → Instance State → Stop
   - Wait for "Stopped" status

2. **Change type:**
   - Actions → Instance Settings → Change instance type
   - Select **t3.small**
   - Click **Apply**

3. **Start instance:**
   - Actions → Instance State → Start
   - ⚠️ **IP will change!** Get new public IP

4. **Reconnect and retry:**
   ```bash
   ssh -i ~/Downloads/openclaw-key.pem ubuntu@NEW_PUBLIC_IP
   openclaw onboard
   ```

**Fix B: Add Swap Space (Free Tier)**

If you want to stay on t3.micro:

```bash
# Create 2GB swap file
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make permanent
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Verify
free -h  # Should show 2G swap
```

Then retry onboarding.

### 11.2 SSH Connection Timeout

**Symptom:** Connection hangs or times out.

**Causes & Fixes:**

1. **Wrong IP after resize:**
   - Get new public IP from EC2 dashboard
   - Update SSH command

2. **Security group rules:**
   - Check port 22 is open
   - Verify your IP hasn't changed
   - Update source IP in security group

3. **Instance stopped:**
   - Check EC2 dashboard
   - Start instance if stopped

### 11.3 Host Key Verification Failed

**Symptom:**
```
Host key verification failed.
```

**Cause:** You pressed Enter instead of typing "yes".

**Fix:**
```bash
ssh-keygen -R YOUR_PUBLIC_IP
```

Then reconnect and type `yes` when prompted.

### 11.4 Permission Denied (publickey)

**Symptom:**
```
Permission denied (publickey).
```

**Causes & Fixes:**

1. **Wrong key permissions:**
   ```bash
   chmod 400 ~/Downloads/openclaw-key.pem
   ```

2. **Wrong key file:**
   - Verify you're using the correct .pem file
   - Check the key name matches your instance

3. **Wrong user:**
   - Use `ubuntu@` not `root@` or `ec2-user@`

### 11.5 Bot Not Responding

**Causes & Fixes:**

1. **Gateway not started:**
   ```bash
   sudo systemctl status openclaw-gateway
   ```
   If not running:
   ```bash
   sudo systemctl start openclaw-gateway
   ```

2. **Check logs:**
   ```bash
   sudo journalctl -u openclaw-gateway -f
   ```
   Look for errors

3. **Wrong bot:**
   - Make sure you're messaging YOUR bot
   - Not @BotFather

4. **Token mismatch:**
   ```bash
   cat ~/.openclaw/config.yaml
   ```
   Verify token matches BotFather

5. **Pairing not approved:**
   - Send message to bot
   - Get pairing code
   - Approve with: `openclaw pairing approve telegram CODE`

### 11.6 API Key Errors

**Symptom:**
```
Invalid API key
```

**Fix:**

1. **Check key format:**
   - Anthropic: starts with `sk-ant-`
   - OpenAI: starts with `sk-`

2. **Verify in config:**
   ```bash
   cat ~/.openclaw/config.yaml
   ```

3. **Re-add key:**
   ```bash
   openclaw configure
   ```

### 11.7 Port 18789 Dashboard Not Loading

**Symptom:** Can't access dashboard at http://YOUR_IP:18789

**Causes & Fixes:**

1. **Security group:**
   - Add inbound rule for port 18789
   - Source: Your IP /32

2. **Gateway not running:**
   ```bash
   sudo systemctl status openclaw-gateway
   ```

3. **Wrong IP:**
   - Use public IP, not private IP

---

## Cost Breakdown

### AWS Costs

| Component | Cost | Notes |
|-----------|------|-------|
| **EC2 t3.small** | ~$15/month | 2GB RAM, 24/7 uptime |
| **Storage (8GB)** | ~$1/month | gp3 SSD |
| **Data Transfer** | $0-5/month | First 100GB free |
| **Elastic IP** | Free | While attached to running instance |

**Total AWS:** ~$16-21/month

### Free Tier Note

AWS Free Tier includes:
- 750 hours/month of t2.micro (but we use t3.small, not covered)
- 30GB storage
- 15GB data transfer out

**Tip:** If cost-sensitive, consider:
- Stopping instance when not needed
- Using swap on t3.micro (slower but cheaper)
- Monitoring with AWS Budgets (set $10/month alert)

### AI API Costs

| Provider | Model | Cost | Usage |
|----------|-------|------|-------|
| **Anthropic** | Claude Sonnet 4.5 | $3/$15 per 1M tokens | ~$0.05/conversation |
| **Anthropic** | Claude Opus 4.6 | $15/$75 per 1M tokens | ~$0.25/conversation |
| **OpenAI** | GPT-4o | $2.50/$10 per 1M tokens | ~$0.03/conversation |
| **OpenAI** | GPT-4 | $30/$60 per 1M tokens | ~$0.50/conversation |

**Typical Usage:**
- Light use (10-20 conversations/day): ~$10-15/month
- Moderate use (50-100 conversations/day): ~$30-50/month
- Heavy use (200+ conversations/day): ~$100+/month

### OpenClaw & Telegram

| Service | Cost |
|---------|------|
| OpenClaw | Free (open source) |
| Telegram | Free |

### Total Monthly Cost

| Usage | AWS | AI API | Total |
|-------|-----|--------|-------|
| **Light** | $16-21 | $10-15 | **$26-36** |
| **Moderate** | $16-21 | $30-50 | **$46-71** |
| **Heavy** | $16-21 | $100+ | **$116+** |

**Compare to:**
- ChatGPT Plus: $20/month (limited, no privacy)
- ChatGPT Team: $25/user/month
- Claude Pro: $20/month (limited)

**Self-hosted wins on:**
- Privacy (your data)
- Customization (tools, integrations)
- No rate limits (pay per use)

---

## Production Hardening

### Security Best Practices

1. **Update regularly:**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
   Run monthly or set up `unattended-upgrades`

2. **Firewall:**
   ```bash
   sudo ufw enable
   sudo ufw allow 22/tcp
   sudo ufw allow 18789/tcp  # Optional: dashboard
   sudo ufw reload
   ```

3. **SSH hardening:**
   - Disable password authentication
   - Use SSH keys only
   - Consider changing SSH port from 22

4. **Fail2ban:**
   ```bash
   sudo apt install fail2ban -y
   sudo systemctl enable fail2ban
   sudo systemctl start fail2ban
   ```

### Monitoring

1. **Set up CloudWatch:**
   - EC2 → Monitoring → Enable detailed monitoring
   - Create alarm for CPU > 80%
   - Create alarm for StatusCheckFailed

2. **Log rotation:**
   OpenClaw logs grow over time. Configure logrotate:
   ```bash
   sudo tee /etc/logrotate.d/openclaw << 'EOF'
   /home/ubuntu/.openclaw/logs/*.log {
       daily
       rotate 7
       compress
       delaycompress
       notifempty
       create 0644 ubuntu ubuntu
   }
   EOF
   ```

3. **Disk space alerts:**
   ```bash
   df -h  # Check current usage
   ```

   Set up cron job to alert if >80%:
   ```bash
   crontab -e
   ```
   Add:
   ```
   0 */6 * * * [ $(df -h / | awk 'NR==2 {print $5}' | sed 's/%//') -gt 80 ] && echo "Disk space low" | mail -s "Alert" your@email.com
   ```

### Backup Strategy

1. **Config backup:**
   ```bash
   cp ~/.openclaw/config.yaml ~/config-backup.yaml
   ```

2. **EC2 snapshots:**
   - EC2 → Elastic Block Store → Snapshots
   - Create snapshot → Select volume
   - Tag: "openclaw-backup-YYYY-MM-DD"

3. **Automated backups:**
   - AWS Backup service
   - Schedule daily snapshots
   - Retain 7 days

### Scaling Considerations

**When to upgrade:**
- CPU constantly >70%
- Memory usage >80%
- Slow responses
- Multiple bot users

**Upgrade path:**
- t3.medium (2 vCPU, 4GB RAM) - ~$30/month
- t3.large (2 vCPU, 8GB RAM) - ~$60/month

**Multiple bots on one instance:**

t3.small can run 2-3 bots with light usage:

```bash
# Second bot
openclaw onboard --config ~/.openclaw/config2.yaml

# Create second service
sudo cp /etc/systemd/system/openclaw-gateway.service \
        /etc/systemd/system/openclaw-gateway-2.service

# Edit ExecStart to use config2.yaml
sudo nano /etc/systemd/system/openclaw-gateway-2.service

# Enable and start
sudo systemctl enable openclaw-gateway-2
sudo systemctl start openclaw-gateway-2
```

---

## Quick Reference

### Essential Commands

```bash
# Service management
sudo systemctl status openclaw-gateway
sudo systemctl restart openclaw-gateway
sudo systemctl stop openclaw-gateway
sudo systemctl start openclaw-gateway

# Logs
sudo journalctl -u openclaw-gateway -f
sudo journalctl -u openclaw-gateway --since "1 hour ago"

# Configuration
cat ~/.openclaw/config.yaml
openclaw configure

# Pairing
openclaw pairing list
openclaw pairing approve telegram CODE
openclaw pairing reject SESSION_KEY

# System
df -h                    # Disk space
free -h                  # Memory usage
top                      # Process monitor
htop                     # Better process monitor (install first)
```

### Emergency Fixes

**Bot stopped responding:**
```bash
sudo systemctl restart openclaw-gateway
sudo journalctl -u openclaw-gateway -f
```

**High memory usage:**
```bash
free -h
sudo systemctl restart openclaw-gateway
```

**Can't SSH in:**
```bash
# From AWS Console:
# 1. Reboot instance (Actions → Reboot)
# 2. Check security groups
# 3. Verify key file
```

---

## Additional Resources

- **OpenClaw Documentation:** https://docs.openclaw.ai
- **OpenClaw GitHub:** https://github.com/openclaw/openclaw
- **OpenClaw Discord:** https://discord.com/invite/clawd
- **Anthropic Console:** https://console.anthropic.com
- **Telegram Bot API:** https://core.telegram.org/bots

---

## Support

If you get stuck:

1. **Check logs:**
   ```bash
   sudo journalctl -u openclaw-gateway -f
   ```

2. **Search Discord:** Many issues already solved

3. **Create GitHub issue:** Include logs and config (redact tokens!)

4. **Hire expert:** Contact consulting services

---

**🦞 Happy self-hosting!**

This guide was created through real deployment experience. Every issue documented here was actually encountered and solved.

Last updated: 2026-02-13
