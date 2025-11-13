# IPFS Best Practices

This guide covers best practices for deploying and maintaining your Astro site on IPFS (InterPlanetary File System) with a focus on censorship resistance, privacy, and reliability.

## Table of Contents

- [Introduction to IPFS](#introduction-to-ipfs)
- [Deployment Best Practices](#deployment-best-practices)
- [Pinning Strategies](#pinning-strategies)
- [Content Addressing](#content-addressing)
- [Privacy Considerations](#privacy-considerations)
- [Performance Optimization](#performance-optimization)
- [Censorship Resistance](#censorship-resistance)
- [Troubleshooting](#troubleshooting)

## Introduction to IPFS

IPFS is a peer-to-peer distributed file system that addresses content by what it is, not where it is located.

### Key Concepts

- **CID (Content Identifier):** Cryptographic hash of your content (e.g., `QmXyz...`)
- **Pinning:** Keeping content available on IPFS
- **Gateway:** HTTP interface to access IPFS content
- **IPNS:** Mutable pointers to IPFS content
- **DNSLink:** Link domain names to IPFS content

## Deployment Best Practices

### 1. Build Optimization

Before deploying to IPFS, optimize your build:

```bash
# Build with production settings
npm run build

# Verify build output
ls -lh dist/

# Check for common issues
find dist/ -type f -name "*.map" # Remove source maps if needed
```

### 2. Verify Static Output

Ensure your site is truly static:

```bash
# Check astro.config.mjs
cat astro.config.mjs
# Should contain: output: 'static'

# Preview locally
npm run preview
# Verify all pages work without server-side rendering
```

### 3. Content Addressing Considerations

IPFS uses content addressing, which has implications:

**Immutability:**
- Each deployment creates a new CID
- Old CIDs remain accessible (if pinned)
- No "updating" files in-place

**Best Practices:**
- Use IPNS for a consistent, updateable address
- Implement versioning in your content
- Document CID history for auditing

### 4. Using Alternate Futures CLI

Deploy with the `af` CLI:

```bash
# Initialize site configuration
af sites init

# Deploy
af sites deploy

# Expected output:
# ✓ Success! Deployed!
# > Site IPFS CID: QmXyz...
# > Gateway URL: https://ipfs.io/ipfs/QmXyz...
```

### 5. Verify Deployment

After deployment, verify:

```bash
# Check CID directly
af sites status

# Test via multiple gateways
curl -I https://ipfs.io/ipfs/YOUR-CID
curl -I https://dweb.link/ipfs/YOUR-CID
curl -I https://cloudflare-ipfs.com/ipfs/YOUR-CID

# Verify content integrity
ipfs dag stat YOUR-CID
```

## Pinning Strategies

Pinning ensures your content remains available on IPFS.

### Why Pin?

Without pinning:
- Content may be garbage collected
- Availability depends on transient nodes
- Site may become unreachable

### Pinning Options

#### 1. Self-Pinning (Highest Reliability)

Run your own IPFS node:

```bash
# Install IPFS
# See: https://docs.ipfs.tech/install/

# Initialize
ipfs init

# Start daemon
ipfs daemon

# Pin your content
ipfs pin add YOUR-CID
```

**Advantages:**
- Full control
- No third-party dependencies
- Maximum censorship resistance

**Disadvantages:**
- Requires infrastructure
- Ongoing maintenance
- Bandwidth costs

#### 2. Alternate Futures Pinning

Built-in pinning via AF:

```bash
# Pin is automatic with deployment
af sites deploy

# Verify pinning status
af sites pins list
```

#### 3. Multiple Pinning Services

Use multiple services for redundancy:

- [Pinata](https://pinata.cloud/)
- [Web3.Storage](https://web3.storage/)
- [Filebase](https://filebase.com/)
- [Estuary](https://estuary.tech/)

```bash
# Example: Pin to Pinata
curl -X POST "https://api.pinata.cloud/pinning/pinByHash" \
  -H "Authorization: Bearer YOUR_JWT" \
  -H "Content-Type: application/json" \
  -d '{"hashToPin": "YOUR-CID"}'
```

#### 4. Collaborative Pinning

Ask the community to pin:

```markdown
# In your README:
Help keep this site available!

Pin this CID: QmXyz...

ipfs pin add QmXyz...
```

### Recommended Strategy

For maximum availability:

1. **Primary:** Self-hosted IPFS node
2. **Secondary:** Alternate Futures pinning
3. **Tertiary:** Third-party pinning service
4. **Community:** Encourage user pinning

## Content Addressing

### Understanding CIDs

```
QmXyz...
│ └── Base58 encoded hash
└── Multihash prefix (hash algorithm + length)
```

**Properties:**
- Deterministic (same content = same CID)
- Verifiable (content matches CID)
- Permanent (CID never changes for that content)

### IPNS (Mutable Pointers)

IPNS allows updating content without changing the address:

```bash
# Create IPNS name
ipfs key gen mysite

# Publish CID to IPNS
ipfs name publish --key=mysite YOUR-CID

# Access via IPNS
https://ipfs.io/ipns/YOUR-IPNS-KEY

# Update (publish new CID to same IPNS name)
ipfs name publish --key=mysite NEW-CID
```

**When to use:**
- Sites that update frequently
- When you need a stable address
- For user-facing URLs

**Limitations:**
- Slower resolution than direct CID
- Requires key management
- TTL (time-to-live) caching

### DNSLink

Point a domain to IPFS content:

```bash
# Add TXT record to your domain:
_dnslink.yourdomain.com TXT "dnslink=/ipfs/YOUR-CID"

# Or for IPNS:
_dnslink.yourdomain.com TXT "dnslink=/ipns/YOUR-IPNS-KEY"

# Access via domain:
https://yourdomain.com (via DNSLink-aware gateway)
```

**With Alternate Futures:**

```bash
# Create custom domain
af domains create yourdomain.com

# Link to your site
af domains attach yourdomain.com --site YOUR-SITE-ID
```

## Privacy Considerations

### Gateway Privacy

Public gateways may compromise privacy:

**What gateways can see:**
- IP addresses
- Access times
- Requested CIDs
- User agents

**Mitigation:**

1. **Run your own gateway:**
   ```bash
   ipfs daemon
   # Access via: http://localhost:8080/ipfs/YOUR-CID
   ```

2. **Use Tor:**
   ```bash
   # Access IPFS via Tor
   torsocks ipfs cat /ipfs/YOUR-CID
   ```

3. **Use VPN:**
   - Hide IP from gateway operators
   - Choose privacy-focused VPN

4. **Privacy-focused gateways:**
   - Research gateway privacy policies
   - Prefer gateways with no-logging policies

### Network Privacy

IPFS is a public network:

**Potential privacy concerns:**
- DHT queries are visible
- Connected peers can see your IP
- Hosting announces content publicly

**Mitigations:**

1. **Private IPFS networks:**
   ```bash
   # Create swarm.key for private network
   # Only peers with same swarm.key can connect
   ```

2. **Content encryption:**
   ```bash
   # Encrypt before adding to IPFS
   gpg --encrypt --recipient you@example.com file.txt
   ipfs add file.txt.gpg
   ```

3. **Tor integration:**
   ```bash
   # Route IPFS through Tor
   ipfs config --json Addresses.Swarm '["/ip4/127.0.0.1/tcp/4001", "/ip4/127.0.0.1/tcp/4002/ws"]'
   ```

### Content Privacy

**Remember:**
- IPFS content is public by default
- CIDs are discoverable
- Content is permanent once published

**Best practices:**
- Never publish secrets or credentials
- Assume all content is public
- Use encryption for sensitive data
- Audit content before publishing

## Performance Optimization

### 1. File Size Optimization

```bash
# Compress images
# Use tools like: imageoptim, squoosh, etc.

# Minify assets (already done by Astro)

# Check size
du -sh dist/
```

### 2. Chunking

IPFS chunks large files automatically:

- Default chunk size: 256 KB
- Optimal for distribution
- Enables partial fetching

### 3. Gateway Selection

Different gateways have different performance:

```javascript
// Test gateway speed
const gateways = [
  'https://ipfs.io/ipfs/',
  'https://dweb.link/ipfs/',
  'https://cloudflare-ipfs.com/ipfs/',
  'https://gateway.pinata.cloud/ipfs/'
];

// Benchmark and choose fastest
```

### 4. Preloading

Add critical resources to HTML:

```html
<link rel="preload" href="/critical.css" as="style">
<link rel="preload" href="/critical.woff2" as="font" crossorigin>
```

### 5. Caching

Set appropriate cache headers (if using custom gateway):

```
Cache-Control: public, max-age=31536000, immutable
```

## Censorship Resistance

### Strategies for Maximum Availability

#### 1. Distributed Pinning

Pin to multiple independent locations:
- Different countries
- Different jurisdictions
- Different organizations

#### 2. DHT Redundancy

IPFS DHT provides redundancy:
- Content advertised to multiple peers
- No single point of failure
- Automatic peer discovery

#### 3. Mirror Sites

Create traditional mirrors as fallback:

```markdown
# Primary
https://yourdomain.com

# IPFS CID
https://ipfs.io/ipfs/QmXyz...

# IPNS
https://ipfs.io/ipns/YOUR-KEY

# Traditional Mirror
https://mirror.yourdomain.com
```

#### 4. Onion Service (Tor)

Serve via Tor hidden service:

```bash
# Configure Tor hidden service
# Point to local IPFS gateway
# Provides .onion address
```

#### 5. Content Seeding

Encourage users to seed:

```html
<!-- Add to your site -->
<p>
  Help keep this site censorship-resistant!
  Pin this content: <code>QmXyz...</code>
</p>

<pre>ipfs pin add QmXyz...</pre>
```

### Monitoring Availability

```bash
# Check DHT providers
ipfs dht findprovs YOUR-CID

# Monitor gateway availability
for gateway in ipfs.io dweb.link cloudflare-ipfs.com; do
  echo "Testing $gateway..."
  curl -sI "https://$gateway/ipfs/YOUR-CID" | head -n1
done
```

## Troubleshooting

### Common Issues

#### Issue: Content Not Resolving

**Symptoms:** CID doesn't load via gateway

**Solutions:**
1. Verify CID is pinned: `ipfs pin ls | grep YOUR-CID`
2. Check DHT providers: `ipfs dht findprovs YOUR-CID`
3. Try different gateway
4. Wait for DHT propagation (up to 24 hours)

#### Issue: Slow Load Times

**Solutions:**
1. Use CDN-backed gateway (Cloudflare, Pinata)
2. Optimize file sizes
3. Enable browser caching
4. Use IPNS with high TTL

#### Issue: Updated Content Not Showing

**Solutions:**
1. Clear browser cache
2. Verify new CID is different
3. Check IPNS publishing: `ipfs name resolve YOUR-IPNS-KEY`
4. Wait for IPNS propagation

#### Issue: Gateway Blocking

**Symptoms:** Gateway refuses to serve content

**Solutions:**
1. Use alternative gateway
2. Run own gateway
3. Check content for ToS violations
4. Contact gateway operator

### Debug Commands

```bash
# Check IPFS daemon status
ipfs id

# List pinned content
ipfs pin ls --type=recursive

# Check routing table
ipfs dht query YOUR-CID

# Verify content
ipfs object stat YOUR-CID

# Manual garbage collection
ipfs repo gc
```

## Advanced Topics

### Continuous Deployment

Automate IPFS deployment:

```yaml
# .github/workflows/deploy.yml
name: Deploy to IPFS
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm ci
      - run: npm run build
      - run: af sites deploy
        env:
          AF_TOKEN: ${{ secrets.AF_TOKEN }}
```

### Content Integrity

Verify content hasn't been tampered with:

```bash
# Calculate CID locally
ipfs add -n -r dist/

# Compare with published CID
# Should match exactly
```

### Blockchain Integration

Link IPFS CIDs on blockchain for:
- Timestamping
- Version history
- Decentralized registry

## Resources

- [IPFS Documentation](https://docs.ipfs.tech/)
- [Alternate Futures Docs](https://docs.alternatefutures.ai/)
- [IPFS Best Practices (official)](https://docs.ipfs.tech/how-to/)
- [Awesome IPFS](https://awesome.ipfs.tech/)

## Questions?

- Open an issue on GitHub
- Check Alternate Futures documentation
- Join IPFS community forums

---

**Remember:** IPFS is a powerful tool for censorship resistance, but it requires understanding and proper configuration. Take time to learn the system and implement redundancy for critical content.
