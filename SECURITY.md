# Security Policy

## Our Commitment

Security and privacy are foundational to this project. We are committed to:

- Maintaining a secure codebase
- Responding promptly to security issues
- Protecting user privacy
- Transparency in our security practices

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| Latest  | :white_check_mark: |
| < Latest| :x:                |

We recommend always using the latest version of this template for the most up-to-date security patches.

## Security Features

This template implements several security best practices:

### Content Security Policy (CSP)
- Restricts resource loading to prevent XSS attacks
- Blocks inline scripts and styles (where possible)
- Prevents data exfiltration to third parties

### Privacy-First Architecture
- No analytics or tracking
- No external dependencies that phone home
- All resources hosted locally
- No cookies or persistent storage (by default)

### Censorship Resistance
- Static site generation (no server vulnerabilities)
- IPFS deployment (distributed, no single point of failure)
- No reliance on centralized services

### Dependency Security
- Regular dependency audits
- Minimal dependency footprint
- Only well-maintained, audited packages

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub issues.**

### Responsible Disclosure

We follow responsible disclosure practices. If you discover a security vulnerability:

1. **Contact Information**
   - Email: [INSERT SECURITY CONTACT EMAIL]
   - PGP Key: [INSERT PGP KEY ID OR LINK] (optional but recommended)
   - For anonymous reports, consider using a temporary encrypted email service

2. **What to Include**
   - Type of vulnerability
   - Full paths of source file(s) related to the vulnerability
   - Location of the affected source code (tag/branch/commit or direct URL)
   - Step-by-step instructions to reproduce the issue
   - Proof-of-concept or exploit code (if possible)
   - Impact assessment
   - Suggested fix (if you have one)

3. **Response Timeline**
   - **Initial Response**: Within 48 hours
   - **Status Update**: Within 7 days
   - **Fix Timeline**: Depends on severity (see below)
   - **Public Disclosure**: After fix is released

### Severity Levels

#### Critical (Fix within 24-48 hours)
- Remote code execution
- Authentication bypass
- Data exfiltration vulnerabilities
- XSS that leads to account compromise

#### High (Fix within 1 week)
- XSS vulnerabilities
- CSRF vulnerabilities
- Privacy leaks
- Dependency vulnerabilities with known exploits

#### Medium (Fix within 2 weeks)
- Security misconfigurations
- Information disclosure
- Minor privacy issues

#### Low (Fix within 1 month)
- Security improvements
- Defense-in-depth measures
- Best practice implementations

## Security Best Practices for Users

### When Using This Template

1. **Keep Dependencies Updated**
   ```bash
   npm audit
   npm update
   ```

2. **Review Generated Code**
   - Don't blindly trust any template
   - Audit the code before deployment
   - Understand what you're deploying

3. **Secure Your Development Environment**
   - Use strong authentication for git repositories
   - Protect your private keys
   - Use encrypted connections

4. **IPFS Deployment Security**
   - Verify content hashes match expected values
   - Pin important content to multiple nodes
   - Consider using IPNS for mutable pointers

5. **Content Security**
   - Never commit secrets to git
   - Use environment variables for sensitive data
   - Review `.gitignore` to prevent accidental exposure

### When Modifying This Template

1. **Before Adding Dependencies**
   - Check the package's reputation and maintenance status
   - Review the package's permissions and network activity
   - Verify licensing compatibility (GPL-3.0)
   - Check for known vulnerabilities: `npm audit`

2. **Before Adding Features**
   - Consider privacy implications
   - Maintain static site generation
   - Avoid server-side dependencies
   - Document security considerations

3. **Testing**
   - Test Content Security Policy compliance
   - Verify no external resources are loaded
   - Check for XSS vulnerabilities
   - Validate all user inputs (if applicable)

## Security Audit History

| Date | Auditor | Type | Findings | Status |
|------|---------|------|----------|--------|
| TBD  | TBD     | TBD  | TBD      | TBD    |

## Known Security Considerations

### Out of Scope
The following are considered out of scope for this template:

- **Server-side security**: This is a static site template
- **DDoS protection**: Handle at infrastructure level
- **Physical security**: User responsibility
- **Social engineering**: User awareness and training

### Threat Model
See [docs/THREAT-MODEL.md](docs/THREAT-MODEL.md) for detailed threat analysis.

## Security-Related Configuration

### Recommended HTTP Headers

When serving this site, configure your server/gateway with:

```
Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; font-src 'self'; connect-src 'self'; frame-ancestors 'none'; base-uri 'self'; form-action 'self'
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Referrer-Policy: no-referrer
Permissions-Policy: geolocation=(), microphone=(), camera=()
```

### IPFS Gateway Considerations

- Public gateways may log access (privacy concern)
- Run your own gateway for maximum privacy
- Consider using Tor or VPN when accessing IPFS content

## Cryptographic Verification

### Verifying Releases

1. Check git tag signatures
2. Verify commit signatures from trusted maintainers
3. Compare checksums of downloaded files

### PGP Key for Maintainers

Maintainer PGP keys should be published:
- On public keyservers
- In this repository (`.github/KEYS` file)
- On personal websites

## Bug Bounty Program

Currently, we do not have a formal bug bounty program. However, we deeply appreciate security researchers who help keep this project secure and will publicly acknowledge contributors (with permission).

## Acknowledgments

We thank the following security researchers for their responsible disclosure:

- [List will be populated as vulnerabilities are reported and fixed]

## Additional Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [IPFS Security Considerations](https://docs.ipfs.tech/concepts/security/)
- [Web Application Security Testing](https://owasp.org/www-project-web-security-testing-guide/)

## Questions?

For non-security questions, use GitHub Issues or Discussions.

For security concerns, follow the responsible disclosure process above.

---

Last updated: 2025-11-12
