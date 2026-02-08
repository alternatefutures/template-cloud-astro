# Mirroring Guide

This guide explains how to mirror sites built with this template to increase availability, censorship resistance, and redundancy.

## Why Mirror?

Mirroring provides:

- **Censorship Resistance:** Multiple hosts make takedown difficult
- **High Availability:** Site remains accessible if one mirror fails
- **Geographic Distribution:** Faster access for users worldwide
- **Community Resilience:** Distributed ownership and control
- **Disaster Recovery:** Backup in case of primary failure

## Types of Mirroring

### 1. IPFS Pinning (Recommended)

The easiest and most effective method for this template.

#### Quick Start

```bash
# Install IPFS
# See: https://docs.ipfs.tech/install/

# Initialize IPFS
ipfs init

# Start daemon
ipfs daemon

# Pin the content
ipfs pin add QmYOUR-CID-HERE

# Verify
ipfs pin ls | grep QmYOUR-CID-HERE
```

#### Keep Your Node Running

For persistent mirroring, keep your IPFS daemon running:

**Using systemd (Linux):**

```bash
# Create service file
sudo nano /etc/systemd/system/ipfs.service
```

```ini
[Unit]
Description=IPFS Daemon
After=network.target

[Service]
Type=simple
User=YOUR_USER
ExecStart=/usr/local/bin/ipfs daemon
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```bash
# Enable and start
sudo systemctl enable ipfs
sudo systemctl start ipfs
```

**Using Docker:**

```bash
docker run -d \
  --name ipfs-node \
  -p 4001:4001 \
  -p 8080:8080 \
  -v /path/to/ipfs/data:/data/ipfs \
  ipfs/go-ipfs:latest

# Pin content
docker exec ipfs-node ipfs pin add QmYOUR-CID-HERE
```

**Using launchd (macOS):**

```xml
<!-- ~/Library/LaunchAgents/io.ipfs.daemon.plist -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>io.ipfs.daemon</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/local/bin/ipfs</string>
    <string>daemon</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <true/>
</dict>
</plist>
```

```bash
launchctl load ~/Library/LaunchAgents/io.ipfs.daemon.plist
```

### 2. Third-Party Pinning Services

Use pinning services for hassle-free mirroring:

#### Pinata

```bash
# Via web interface: pinata.cloud
# Or via API:

curl -X POST "https://api.pinata.cloud/pinning/pinByHash" \
  -H "Authorization: Bearer YOUR_PINATA_JWT" \
  -H "Content-Type: application/json" \
  -d '{
    "hashToPin": "QmYOUR-CID-HERE",
    "pinataMetadata": {
      "name": "Site Mirror"
    }
  }'
```

#### Web3.Storage

```bash
# Install CLI
npm install -g @web3-storage/w3cli

# Login
w3 login your@email.com

# Upload (creates automatic pin)
w3 upload dist/
```

#### Filebase

1. Sign up at filebase.com
2. Create IPFS bucket
3. Upload content via web interface or S3 API

### 3. Git Repository Mirror

Mirror the source code for rebuilding:

#### On GitHub

```bash
# Fork the repository on GitHub
# Enable GitHub Actions for automatic deployments
```

#### On GitLab

```bash
# Mirror to GitLab
git remote add gitlab git@gitlab.com:YOUR-USERNAME/REPO.git
git push gitlab main
```

#### On Gitea (Self-Hosted)

```bash
# Run your own Gitea instance
# Mirror repository there
git remote add gitea https://your-gitea.com/YOUR-USERNAME/REPO.git
git push gitea main
```

#### On Radicle (Decentralized)

```bash
# Install Radicle
# See: https://radicle.xyz/

# Initialize
rad init

# Push to Radicle network
rad push
```

### 4. Traditional Web Mirror

Host a traditional copy as fallback:

#### Static Hosting Options

- **Netlify:** Free tier, automatic HTTPS
- **Vercel:** Free tier, edge network
- **GitHub Pages:** Free, easy setup
- **Cloudflare Pages:** Free, fast CDN

#### Example: GitHub Pages

```bash
# Create gh-pages branch
git checkout -b gh-pages
npm run build
cp -r dist/* .
git add .
git commit -m "Deploy to GitHub Pages"
git push origin gh-pages

# Enable in repository settings
# Site will be at: https://USERNAME.github.io/REPO
```

## Creating a Mirror Network

### Coordinated Mirroring

Organize with others to mirror content:

1. **Create a Mirror List**

```markdown
# MIRRORS.md

## Official Mirrors

- Primary: https://yoursite.com
- IPFS: https://ipfs.io/ipfs/QmXyz...
- Mirror 1: https://mirror1.example.com (John Doe)
- Mirror 2: https://mirror2.example.com (Jane Smith)
- Tor: http://xyz123...onion

## How to Become a Mirror

See [MIRRORING.md](MIRRORING.md)
```

2. **Provide Mirror Badge**

```html
<!-- Badge for mirrors to display -->
<a href="https://yoursite.com/mirrors">
  <img src="/mirror-badge.svg" alt="Official Mirror">
</a>
```

3. **Verification**

```bash
# Mirrors should prove authenticity
# Compare checksums:
sha256sum dist/* > checksums.txt

# Sign with GPG
gpg --sign checksums.txt

# Mirrors verify:
gpg --verify checksums.txt.gpg
sha256sum -c checksums.txt
```

### Automated Mirror Updates

Set up automatic updates when source changes:

#### Using GitHub Actions (on mirror repository)

```yaml
# .github/workflows/update-mirror.yml
name: Update Mirror

on:
  schedule:
    - cron: '0 */6 * * *' # Every 6 hours
  workflow_dispatch: # Manual trigger

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          repository: ORIGINAL/REPO
          ref: main

      - name: Setup Node
        uses: actions/setup-node@v2
        with:
          node-version: '18'

      - name: Build
        run: |
          npm ci
          npm run build

      - name: Deploy to IPFS
        run: |
          npm install -g @web3-storage/w3cli
          w3 upload dist/
        env:
          W3_TOKEN: ${{ secrets.W3_TOKEN }}
```

#### Using Cron (on your server)

```bash
#!/bin/bash
# mirror-update.sh

# Pull latest
cd /path/to/repo
git pull origin main

# Build
npm ci
npm run build

# Update IPFS pin
NEW_CID=$(ipfs add -r -Q dist/)
ipfs pin add $NEW_CID

# Unpin old (optional)
# ipfs pin rm $OLD_CID

echo "Updated to CID: $NEW_CID"
```

```bash
# Add to crontab
crontab -e

# Run every 6 hours
0 */6 * * * /path/to/mirror-update.sh
```

## Monitoring Your Mirror

### Check Mirror Status

```bash
# IPFS pin status
ipfs pin ls | grep QmYOUR-CID

# Disk usage
ipfs repo stat

# Connected peers
ipfs swarm peers | wc -l

# Gateway accessibility
curl -I https://ipfs.io/ipfs/QmYOUR-CID
```

### Automated Monitoring

Create a monitoring script:

```bash
#!/bin/bash
# monitor-mirror.sh

CID="QmYOUR-CID-HERE"
GATEWAYS=(
  "ipfs.io"
  "dweb.link"
  "cloudflare-ipfs.com"
)

echo "Monitoring IPFS mirror for CID: $CID"
echo "---"

# Check if pinned
if ipfs pin ls | grep -q $CID; then
  echo "✓ Content is pinned locally"
else
  echo "✗ WARNING: Content is not pinned!"
  exit 1
fi

# Check gateway accessibility
for gateway in "${GATEWAYS[@]}"; do
  status=$(curl -s -o /dev/null -w "%{http_code}" \
    "https://$gateway/ipfs/$CID")

  if [ "$status" -eq 200 ]; then
    echo "✓ Accessible via $gateway"
  else
    echo "✗ Not accessible via $gateway (HTTP $status)"
  fi
done

# Check DHT providers
provider_count=$(ipfs dht findprovs $CID | wc -l)
echo "---"
echo "DHT providers: $provider_count"

if [ "$provider_count" -lt 3 ]; then
  echo "⚠ WARNING: Low provider count!"
fi
```

```bash
# Run via cron every hour
0 * * * * /path/to/monitor-mirror.sh >> /var/log/ipfs-monitor.log
```

## Mirror Best Practices

### Geographic Diversity

- Mirror in different countries
- Use different jurisdictions
- Avoid single points of failure

### Infrastructure Diversity

- Use different hosting providers
- Mix cloud and self-hosted
- Combine IPFS and traditional hosting

### Update Coordination

- Subscribe to update notifications
- Monitor source repository
- Automate updates where possible

### Resource Limits

Set storage limits on IPFS nodes:

```bash
# Limit repo size
ipfs config Datastore.StorageMax 50GB

# Garbage collect old content
ipfs repo gc
```

## Becoming a Public Mirror

### Requirements

1. **Reliable Uptime**
   - 99%+ availability
   - Monitoring and alerts
   - Maintenance windows announced

2. **Adequate Bandwidth**
   - Sufficient for expected traffic
   - No throttling or caps

3. **Security**
   - HTTPS enabled
   - Firewall configured
   - Regular security updates

4. **Verification**
   - Contact original maintainer
   - Prove control of domain/server
   - Sign commitment with GPG key

### Application Process

1. Set up mirror
2. Test for 30 days
3. Submit mirror information:
   ```markdown
   - URL: https://mirror.example.com
   - IPFS Node ID: QmXyz...
   - Location: City, Country
   - Maintainer: Your Name
   - Contact: email@example.com
   - GPG Key: ABCD1234
   - Uptime Commitment: 99.9%
   ```
4. Await verification
5. Get added to official mirror list

## Emergency Mirroring

If the original site is threatened:

### Rapid Response

```bash
# Quick mirror setup (5 minutes)

# 1. Clone
git clone https://github.com/ORIGINAL/REPO.git
cd REPO

# 2. Build
npm ci
npm run build

# 3. Deploy to IPFS
ipfs add -r dist/ | tail -n1

# 4. Announce new CID
# Share on social media, forums, etc.
```

### Deadman's Switch

Set up automatic mirroring if original goes down:

```bash
#!/bin/bash
# deadman-switch.sh

ORIGINAL_URL="https://yoursite.com"

# Check if original is down
if ! curl -sf "$ORIGINAL_URL" > /dev/null; then
  echo "Original site is down! Activating mirror..."

  # Deploy mirror
  /path/to/deploy-mirror.sh

  # Send alerts
  echo "Original site down" | mail -s "Mirror Activated" admin@example.com
fi
```

## Coordination Tools

### Communication Channels

- **Matrix/Element:** Encrypted group chat
- **IRC:** Traditional but effective
- **Signal:** Secure messaging
- **PGP Email:** Verified communication

### Mirror Registry

Maintain a decentralized registry:

```json
{
  "site": "yoursite.com",
  "cid": "QmXyz...",
  "mirrors": [
    {
      "url": "https://mirror1.com",
      "ipfs_id": "QmAbc...",
      "location": "US",
      "status": "active",
      "last_update": "2025-11-12T10:00:00Z"
    }
  ]
}
```

Publish registry to IPFS for decentralized discovery.

## Legal Considerations

### Hosting in Multiple Jurisdictions

- Understand local laws
- Respect intellectual property (GPL-3.0 compliance)
- Consider liability

### DMCA and Takedown Requests

IPFS content is resistant to takedown, but:
- Gateway operators may comply
- Traditional mirrors may receive notices
- Know your rights and responsibilities

### Transparency

If you receive a takedown request:
- Document it
- Share with community (if legal)
- Consider jurisdiction shopping

## Resources

- [IPFS Docs: Persistence](https://docs.ipfs.tech/concepts/persistence/)
- [Awesome IPFS: Pinning Services](https://awesome.ipfs.tech/categories/pinning-services/)
- [Internet Archive](https://archive.org/) - For traditional archiving

## Questions?

- Open an issue for mirror coordination
- Join community discussions
- See [CONTRIBUTING.md](../CONTRIBUTING.md)

---

**Thank you for helping keep the web open, accessible, and censorship-resistant!**
