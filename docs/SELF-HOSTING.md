# Self-Hosting Guide

This guide covers how to self-host your Astro + Alternate Futures site for maximum control, privacy, and censorship resistance.

## Why Self-Host?

**Benefits:**
- Complete control over infrastructure
- Maximum privacy (no third-party dependencies)
- Custom domain and branding
- No vendor lock-in
- Educational value
- Support censorship-resistant web

**Trade-offs:**
- Requires technical knowledge
- Infrastructure maintenance
- Bandwidth costs
- 24/7 uptime responsibility

## Option 1: Self-Hosted IPFS Node

### Prerequisites

- Linux server (Ubuntu 22.04 recommended)
- 2+ GB RAM
- 50+ GB storage
- Public IP address or domain
- Basic command line knowledge

### Installation

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install IPFS
wget https://dist.ipfs.tech/kubo/v0.24.0/kubo_v0.24.0_linux-amd64.tar.gz
tar -xvzf kubo_v0.24.0_linux-amd64.tar.gz
cd kubo
sudo bash install.sh

# Verify installation
ipfs --version

# Initialize IPFS
ipfs init

# Configure for server
ipfs config profile apply server
```

### Configuration

```bash
# Set storage limit
ipfs config Datastore.StorageMax 50GB

# Configure API access (local only for security)
ipfs config Addresses.API /ip4/127.0.0.1/tcp/5001

# Configure gateway
ipfs config Addresses.Gateway /ip4/0.0.0.0/tcp/8080

# Disable unnecessary services
ipfs config --json Experimental.FilestoreEnabled false
ipfs config --json Experimental.UrlstoreEnabled false
```

### Run as System Service

Create systemd service:

```bash
sudo nano /etc/systemd/system/ipfs.service
```

```ini
[Unit]
Description=IPFS Daemon
After=network.target

[Service]
Type=simple
User=YOUR_USER
Environment="IPFS_PATH=/home/YOUR_USER/.ipfs"
ExecStart=/usr/local/bin/ipfs daemon
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```bash
# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable ipfs
sudo systemctl start ipfs

# Check status
sudo systemctl status ipfs
```

### Deploy Your Site

```bash
# Build your site
npm run build

# Add to IPFS
CID=$(ipfs add -r -Q dist/)
echo "Site CID: $CID"

# Pin it
ipfs pin add $CID

# Access via gateway
curl http://localhost:8080/ipfs/$CID
```

### Set Up IPNS

```bash
# Generate key
ipfs key gen --type=rsa --size=2048 mysite

# Publish to IPNS
ipfs name publish --key=mysite $CID

# Get IPNS address
IPNS_KEY=$(ipfs key list -l | grep mysite | awk '{print $1}')
echo "IPNS: /ipns/$IPNS_KEY"

# Update when deploying new version
ipfs name publish --key=mysite $NEW_CID
```

### Set Up Reverse Proxy (Nginx)

```bash
sudo apt install nginx certbot python3-certbot-nginx -y
```

```nginx
# /etc/nginx/sites-available/ipfs-gateway
server {
    listen 80;
    server_name yourdomaincom;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Security headers
        add_header X-Frame-Options "DENY" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header Referrer-Policy "no-referrer" always;
        add_header Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:;" always;
    }
}
```

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/ipfs-gateway /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

# Get SSL certificate
sudo certbot --nginx -d yourdomain.com
```

### DNSLink Setup

Add TXT record to your domain:

```
_dnslink.yourdomain.com TXT "dnslink=/ipns/YOUR_IPNS_KEY"
```

Access via: `https://yourdomain.com/ipns/yourdomain.com`

## Option 2: Docker Deployment

### docker-compose.yml

```yaml
version: '3.8'

services:
  ipfs:
    image: ipfs/kubo:latest
    container_name: ipfs
    ports:
      - "4001:4001"     # Swarm
      - "5001:5001"     # API (localhost only in production)
      - "8080:8080"     # Gateway
    volumes:
      - ./ipfs-data:/data/ipfs
      - ./ipfs-staging:/export
    environment:
      - IPFS_PROFILE=server
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    container_name: nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - ./certs:/etc/nginx/certs
    depends_on:
      - ipfs
    restart: unless-stopped
```

```bash
# Start services
docker-compose up -d

# Add content
docker exec ipfs ipfs add -r /export/dist

# Check status
docker-compose ps
docker-compose logs -f ipfs
```

## Option 3: Traditional Static Hosting

### Using Nginx

```nginx
server {
    listen 80;
    server_name yourdomain.com;
    root /var/www/yoursite;
    index index.html;

    # Security headers (same as above)

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Don't cache HTML
    location ~* \.html$ {
        add_header Cache-Control "no-cache, no-store, must-revalidate";
    }
}
```

### Using Apache

```apache
<VirtualHost *:80>
    ServerName yourdomain.com
    DocumentRoot /var/www/yoursite

    <Directory /var/www/yoursite>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # Security headers
        Header always set X-Frame-Options "DENY"
        Header always set X-Content-Type-Options "nosniff"
        Header always set Referrer-Policy "no-referrer"
    </Directory>

    # Enable compression
    <IfModule mod_deflate.c>
        AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript
    </IfModule>
</VirtualHost>
```

## Option 4: VPS with Automated Deployment

### Initial Setup

```bash
# SSH into VPS
ssh user@your-vps-ip

# Install dependencies
sudo apt update
sudo apt install -y nodejs npm nginx git

# Clone your repository
git clone https://github.com/yourusername/yoursite.git
cd yoursite

# Build
npm ci
npm run build

# Copy to web root
sudo cp -r dist/* /var/www/html/
```

### Automated Deployment Script

```bash
#!/bin/bash
# deploy.sh

set -e

REPO_DIR="/home/user/yoursite"
WEB_ROOT="/var/www/html"

cd $REPO_DIR

# Pull latest
git pull origin main

# Install deps
npm ci

# Build
npm run build

# Backup current
sudo cp -r $WEB_ROOT $WEB_ROOT.backup.$(date +%Y%m%d%H%M%S)

# Deploy
sudo rsync -av --delete dist/ $WEB_ROOT/

# Reload nginx
sudo systemctl reload nginx

echo "Deployment completed at $(date)"
```

### Webhook for Auto-Deploy

```javascript
// webhook-server.js
const http = require('http');
const crypto = require('crypto');
const { execSync } = require('child_process');

const SECRET = process.env.WEBHOOK_SECRET;
const PORT = 9000;

const server = http.createServer((req, res) => {
  if (req.method === 'POST' && req.url === '/deploy') {
    let body = '';

    req.on('data', chunk => body += chunk);
    req.on('end', () => {
      // Verify signature (GitHub webhook)
      const signature = req.headers['x-hub-signature-256'];
      const hash = crypto.createHmac('sha256', SECRET).update(body).digest('hex');

      if (`sha256=${hash}` === signature) {
        console.log('Valid webhook received, deploying...');

        try {
          execSync('/home/user/deploy.sh', { stdio: 'inherit' });
          res.writeHead(200);
          res.end('Deployed successfully');
        } catch (error) {
          console.error('Deployment failed:', error);
          res.writeHead(500);
          res.end('Deployment failed');
        }
      } else {
        res.writeHead(401);
        res.end('Invalid signature');
      }
    });
  } else {
    res.writeHead(404);
    res.end('Not found');
  }
});

server.listen(PORT, () => {
  console.log(`Webhook server listening on port ${PORT}`);
});
```

## Monitoring & Maintenance

### Health Checks

```bash
#!/bin/bash
# health-check.sh

# Check if IPFS daemon is running
if ! pgrep -x "ipfs" > /dev/null; then
    echo "IPFS daemon not running!"
    systemctl start ipfs
fi

# Check gateway accessibility
if ! curl -sf http://localhost:8080/ipfs/QmYour-CID > /dev/null; then
    echo "Gateway not responding!"
    systemctl restart ipfs
fi

# Check disk space
USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ "$USAGE" -gt 90 ]; then
    echo "Disk usage critical: ${USAGE}%"
    ipfs repo gc
fi
```

Add to crontab:
```bash
*/5 * * * * /home/user/health-check.sh >> /var/log/ipfs-health.log 2>&1
```

### Backup Strategy

```bash
#!/bin/bash
# backup.sh

BACKUP_DIR="/backups/ipfs"
DATE=$(date +%Y%m%d)

# Backup IPFS config
tar -czf $BACKUP_DIR/ipfs-config-$DATE.tar.gz ~/.ipfs/config

# Backup pinned CIDs
ipfs pin ls > $BACKUP_DIR/pinned-cids-$DATE.txt

# Keep only last 7 days
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete
```

### Update Process

```bash
# Update IPFS
wget https://dist.ipfs.tech/kubo/LATEST_VERSION/kubo_LATEST_VERSION_linux-amd64.tar.gz
tar -xvzf kubo_LATEST_VERSION_linux-amd64.tar.gz
cd kubo
sudo bash install.sh

# Restart service
sudo systemctl restart ipfs

# Update site dependencies
cd /path/to/yoursite
npm update
npm audit fix
npm run build
```

## Security Hardening

### Firewall (UFW)

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp    # SSH
sudo ufw allow 80/tcp    # HTTP
sudo ufw allow 443/tcp   # HTTPS
sudo ufw allow 4001/tcp  # IPFS swarm
sudo ufw enable
```

### Fail2Ban

```bash
sudo apt install fail2ban -y

# Configure for SSH protection
sudo nano /etc/fail2ban/jail.local
```

```ini
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
```

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Regular Updates

```bash
# Set up automatic security updates
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure -plow unattended-upgrades
```

## Troubleshooting

**IPFS daemon won't start:**
```bash
ipfs repo fsck
ipfs repo gc
```

**Gateway not accessible:**
```bash
# Check firewall
sudo ufw status

# Check IPFS config
ipfs config Addresses.Gateway

# Check logs
journalctl -u ipfs -n 100
```

**High resource usage:**
```bash
# Limit connections
ipfs config --json Swarm.ConnMgr.HighWater 100
ipfs config --json Swarm.ConnMgr.LowWater 50

# Restart
sudo systemctl restart ipfs
```

## Cost Estimates

**VPS Hosting:**
- DigitalOcean Droplet: $6-12/month
- Linode: $5-10/month
- Vultr: $6-12/month

**Domain:**
- .com domain: $10-15/year
- .xyz domain: $1-5/year

**Total:** ~$75-150/year

## Resources

- [IPFS Docs](https://docs.ipfs.tech/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Let's Encrypt](https://letsencrypt.org/)
- [DigitalOcean Tutorials](https://www.digitalocean.com/community/tutorials)

---

Need help? Open an issue or consult the community!
