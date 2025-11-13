# Privacy Policy Template

**NOTE:** This is a template privacy policy for sites built with this Astro + Alternate Futures starter kit. Customize it for your specific use case before deployment. Replace `[YOUR SITE NAME]`, `[YOUR CONTACT]`, and other placeholders with your actual information.

---

# Privacy Policy for [YOUR SITE NAME]

**Last Updated:** [DATE]

## Our Commitment to Privacy

At [YOUR SITE NAME], we believe privacy is a fundamental right. This site is built with privacy-by-design principles and deployed on a decentralized, censorship-resistant infrastructure (IPFS).

## Information We Do NOT Collect

We want to be crystal clear about what we **do not** collect:

- ❌ We do NOT use cookies
- ❌ We do NOT use analytics or tracking scripts
- ❌ We do NOT collect personal information
- ❌ We do NOT log your IP address
- ❌ We do NOT track your browsing behavior
- ❌ We do NOT use fingerprinting techniques
- ❌ We do NOT share data with third parties (because we don't collect any)
- ❌ We do NOT serve advertisements

## How This Site Works

### Static Site Architecture

This website is a static site, meaning:

- No server-side code processes your requests
- No databases store information about visitors
- No session data is maintained
- All content is pre-generated and served as-is

### IPFS Deployment

This site is deployed on the InterPlanetary File System (IPFS), a peer-to-peer distributed file system:

- **Content Addressing:** Files are addressed by their cryptographic hash, not by location
- **Distributed Hosting:** Content is distributed across multiple nodes
- **Censorship Resistant:** No central authority can take down the content
- **Permanent:** Content remains available as long as someone hosts it

### Important IPFS Privacy Considerations

While we don't collect data, you should be aware of IPFS network characteristics:

1. **Gateway Logging:**
   - If accessing via a public IPFS gateway (e.g., `ipfs.io/ipfs/[CID]`), the gateway operator may log requests
   - Gateway logs may include IP addresses and access times
   - **Recommendation:** Use a trusted gateway, run your own, or access via Tor

2. **Network Visibility:**
   - IPFS is a public network
   - DHT queries (content lookups) may be observable
   - **Mitigation:** Use VPN, Tor, or privacy-focused IPFS implementations

3. **Content Permanence:**
   - Once published, content cannot be "deleted" from IPFS
   - Content remains available as long as it's pinned somewhere
   - Treat all published content as permanent

## Third-Party Services

We do not integrate any third-party services by default. If you click on external links, you will be subject to those sites' privacy policies.

### External Links

This site may contain links to:
- [LIST EXTERNAL SITES LINKED, e.g., Documentation, GitHub, etc.]

We are not responsible for the privacy practices of external sites.

## Your Rights

Since we collect no personal data, traditional data subject rights (access, rectification, deletion, portability) are not applicable. However:

- **Transparency:** All code is open source (GPL-3.0) for auditing
- **Verification:** You can verify our privacy claims by inspecting the code
- **Control:** You can host your own copy of this site

## Technical Measures

We implement the following technical privacy protections:

### Content Security Policy (CSP)

We use a strict Content Security Policy to prevent:
- Cross-site scripting (XSS) attacks
- Data exfiltration to third parties
- Unauthorized resource loading

### Security Headers

- `X-Content-Type-Options: nosniff` - Prevents MIME sniffing
- `X-Frame-Options: DENY` - Prevents clickjacking
- `Referrer-Policy: no-referrer` - Prevents referrer leakage

### Permissions Policy

We disable access to sensitive browser APIs:
- Geolocation
- Camera and Microphone
- Payment APIs
- USB, Bluetooth
- Motion sensors

## Data Retention

**N/A** - We don't collect data, so there's nothing to retain or delete.

## Children's Privacy

We do not knowingly collect information from anyone, including children under 13 (or applicable age in your jurisdiction).

## International Users

This site can be accessed from anywhere via IPFS. Since we collect no data:
- No cross-border data transfers occur
- GDPR, CCPA, and similar regulations are satisfied by design
- No data processing agreements are necessary

## Changes to This Policy

If our privacy practices change, we will:
1. Update this policy with a new "Last Updated" date
2. Publish the updated version on IPFS
3. Announce changes via [YOUR COMMUNICATION CHANNEL]

Previous versions can be found in our Git history.

## Compliance

### GDPR (General Data Protection Regulation)

- **Lawful Basis:** N/A (no processing)
- **Data Minimization:** Zero data collected
- **Right to Access:** N/A (no data stored)
- **Right to Erasure:** N/A (no data stored)
- **Data Portability:** N/A (no data stored)

### CCPA (California Consumer Privacy Act)

- We do not sell personal information (we don't collect any)
- No opt-out mechanism needed (nothing to opt out of)

## Open Source Transparency

This site is built with the [Astro + Alternate Futures Starter Kit](https://github.com/[YOUR-REPO]), which:

- Is licensed under GPL-3.0
- Can be audited by anyone
- Has undergone privacy audits (see `docs/PRIVACY-AUDIT.md`)
- Contains no proprietary tracking code

## Verification

You can verify our privacy claims by:

1. **Inspecting the Source Code:**
   - GitHub Repository: [YOUR-REPO-URL]
   - License: GPL-3.0
   - Commit History: Fully transparent

2. **Checking Network Activity:**
   - Use browser DevTools to monitor network requests
   - Use privacy analysis tools (Blacklight, PrivacyScore, etc.)
   - Should show zero third-party requests

3. **Auditing Dependencies:**
   ```bash
   npm list
   ```
   - Minimal dependencies
   - All dependencies are open source and auditable

## Contact Us

If you have questions about this privacy policy:

- **Email:** [YOUR CONTACT EMAIL]
- **PGP Key:** [YOUR PGP KEY FINGERPRINT] (optional)
- **GitHub:** [YOUR GITHUB ISSUES URL]

For security vulnerabilities, see our [Security Policy](../SECURITY.md).

## Philosophy

We believe:

- **Privacy is a right, not a privilege**
- **Transparency builds trust** - that's why we're open source
- **Decentralization promotes freedom** - that's why we use IPFS
- **Less is more** - we collect zero data because we don't need it

## Legal

This privacy policy is provided "as-is" and may change without notice. Your use of this site constitutes acceptance of this policy.

**Jurisdiction:** [YOUR JURISDICTION]

**Governing Law:** [APPLICABLE LAW]

---

## For Template Users

If you're using the Astro + Alternate Futures template:

### ✅ This Policy Applies If:

- You haven't modified the template to collect data
- You don't add analytics, tracking, or cookies
- You maintain the static-only architecture
- You don't integrate third-party services that collect data

### ⚠️ You Must Update This Policy If:

- You add forms that collect user input
- You integrate analytics (even privacy-focused ones)
- You add authentication/user accounts
- You use cookies or local storage
- You make any network requests to your own or third-party servers
- You add payment processing
- You integrate social media widgets

### Privacy-Preserving Additions

If you need additional functionality, consider:

- **Analytics:** Self-hosted Plausible or Matomo (with anonymization)
- **Comments:** Self-hosted solutions or encrypted options
- **Forms:** Client-side validation only, or privacy-focused form handlers
- **Authentication:** Decentralized identity solutions

Always update your privacy policy to reflect actual practices.

---

**Remember:** This is a template. Consult with legal counsel for your specific situation and jurisdiction.
