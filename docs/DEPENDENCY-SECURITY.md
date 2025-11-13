# Dependency Security Status

## Overview

This document tracks the security status of dependencies and Dependabot alerts.

**Last Updated:** 2025-11-13

## Current Status

- **npm audit**: 0 vulnerabilities
- **Dependabot Alerts**: Monitoring (auto-closes as dependencies update)
- **Production Dependencies**: Secure
- **Dev Dependencies**: Latest versions with overrides

## Package Overrides

We use npm `overrides` in package.json to force secure versions of transitive dependencies:

```json
{
  "overrides": {
    "braces": "^3.0.3",          // Fixes uncontrolled resource consumption
    "cross-spawn": "^7.0.5",     // Fixes ReDoS vulnerability
    "micromatch": "^4.0.8",      // Fixes ReDoS vulnerability
    "nanoid": "^3.3.8",          // Fixes predictable generation
    "cookie": "^0.7.2",          // Fixes character validation
    "undici": "^6.23.0"          // Fixes multiple security issues
  }
}
```

## Dev-Only vs Runtime Vulnerabilities

### Development/Build-Time Only

The following packages are **only used during build** and do not affect the deployed static site:

- **vite** - Development server and bundler
- **esbuild** - Transpiler (build-time)
- **rollup** - Bundler (build-time)
- **@babel/helpers** - Transpilation (build-time)
- **prismjs** - Syntax highlighting (build-time for markdown)

**Security Note:** Vulnerabilities in these packages generally only affect:
- Local development environments
- CI/CD build processes
- Not the deployed static HTML/CSS/JS

### Runtime Dependencies

- **astro**: Core framework (v5.15.5) - Latest stable
- **devalue**: Serialization (v5.5.0) - Patched version >= 5.3.2
- **dset**: Object mutation (v3.1.4) - Patched version

## Dependabot Alert Strategy

### Auto-Closing Alerts

Many Dependabot alerts will auto-close when:
1. GitHub re-scans the repository (happens periodically)
2. Detects overridden versions in package-lock.json
3. Confirms packages are at patched versions

### Manual Review Required

High/Critical severity alerts in runtime dependencies require immediate attention.

### Development-Only Alerts

Medium/Low severity alerts in dev dependencies are monitored but lower priority since they don't affect production.

## Verification

### Check Current Versions

```bash
# Check specific package versions
npm ls devalue dset prismjs rollup

# Run security audit
npm audit

# Check for outdated packages
npm outdated
```

### Expected Output

```
astro-template@0.0.1
└─┬ astro@5.15.5
  ├── devalue@5.5.0 (>= 5.3.2 required)
  ├── dset@3.1.4 (>= 3.1.4 required)
  └─┬ @astrojs/markdown-remark@6.3.8
    └─┬ @astrojs/prism@3.3.0
      └── prismjs@1.30.0 (>= 1.30.0 required)
```

## Static Site Security

This template generates **static HTML/CSS/JS** files. The security posture of the deployed site is:

### What's in the Deployed Site
- Pre-rendered HTML
- Bundled JavaScript (minimal, for service worker)
- CSS
- Static assets (images, fonts)
- Service worker (for offline support)

### What's NOT in the Deployed Site
- No Node.js runtime
- No development dependencies
- No build tools
- No server-side code

### Security Implications

Build-time vulnerabilities (vite, esbuild, rollup) **do not affect** the deployed static site because:
1. These tools only run during `npm run build`
2. The output (`dist/`) contains no vulnerable code
3. Static files are served as-is (no server processing)

## Monitoring Strategy

### Automated
- GitHub Dependabot scans daily
- Dependabot PRs for security updates
- GitHub Actions security audit workflow

### Manual
- Review Dependabot alerts weekly
- Update Astro when new versions release
- Test builds after dependency updates

## Response Plan

### Critical/High Severity (Runtime)
1. Review alert immediately
2. Test proposed fix
3. Deploy within 24-48 hours

### Medium Severity (Runtime)
1. Review within 1 week
2. Plan update for next release
3. Deploy within 2 weeks

### Low Severity or Dev-Only
1. Review monthly
2. Batch updates
3. Deploy with next feature release

## Known Limitations

### Astro's Internal Dependencies

Some vulnerabilities are in Astro's own dependencies (vite, esbuild, etc.). We cannot directly update these without:
- Waiting for Astro to update them, or
- Forking Astro (not recommended)

**Mitigation:** These are build-time tools and don't affect the static output.

### False Positives

Dependabot may flag packages that:
- Are already patched (waiting for re-scan)
- Are not in our dependency tree
- Have vulnerabilities that don't apply to our use case

## References

- [npm audit Documentation](https://docs.npmjs.com/cli/v10/commands/npm-audit)
- [Dependabot Documentation](https://docs.github.com/en/code-security/dependabot)
- [Astro Security](https://github.com/withastro/astro/security)
- [OWASP Dependency Check](https://owasp.org/www-project-dependency-check/)

## Questions?

Open an issue or see [SECURITY.md](../SECURITY.md) for security reporting.

---

**Note:** This is a living document. Update after each dependency change.
