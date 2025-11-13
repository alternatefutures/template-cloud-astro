# Threat Model

## Overview

This document outlines the threat model for sites built with the Astro + Alternate Futures template, identifying potential threats, mitigations, and residual risks.

## Scope

**In Scope:**
- Template code and configuration
- Static site generation process
- IPFS deployment
- Client-side security
- Privacy protections

**Out of Scope:**
- User-added features or modifications
- Hosting infrastructure security (user's responsibility)
- Physical security
- Social engineering attacks
- DDoS attacks (handled at infrastructure level)

## Trust Boundaries

1. **Template Code** ← You are here
2. **Build Process** (user's environment)
3. **IPFS Network** (distributed, trustless)
4. **Gateway** (varies by provider)
5. **End User Browser** (user's device)

## Assets

### Primary Assets
- **User Privacy**: Most critical asset to protect
- **Content Integrity**: Ensure content isn't tampered with
- **Availability**: Keep site accessible despite censorship attempts

### Secondary Assets
- **Source Code**: Open source (GPL-3.0)
- **Build Artifacts**: Static files in `dist/`
- **Deployment Credentials**: AF tokens, API keys

## Threat Actors

### 1. Mass Surveillance (Nation-State)
- **Capability**: Very High
- **Motivation**: Surveillance, censorship
- **Resources**: Unlimited

### 2. ISP/Gateway Operator
- **Capability**: Medium-High
- **Motivation**: Compliance, profiling
- **Resources**: High

### 3. Malicious Third Parties
- **Capability**: Medium
- **Motivation**: Data theft, malware distribution
- **Resources**: Medium

### 4. Compromised Dependencies
- **Capability**: Varies
- **Motivation**: Supply chain attack
- **Resources**: Varies

## Threats & Mitigations

### T-001: User Tracking
**Description**: Attacker tracks user behavior via analytics or fingerprinting.

**Impact**: High (privacy violation)

**Likelihood**: Low (by design)

**Mitigations**:
- ✅ No analytics code included
- ✅ No third-party scripts
- ✅ Content Security Policy blocks unauthorized requests
- ✅ Privacy audit conducted

**Residual Risk**: Low

---

### T-002: Content Tampering
**Description**: Attacker modifies site content in transit or at rest.

**Impact**: High (integrity violation)

**Likelihood**: Low

**Mitigations**:
- ✅ IPFS content addressing (CID = cryptographic hash)
- ✅ HTTPS for gateway access
- ✅ Subresource Integrity (if using CDNs)
- ⚠️ Users should verify CIDs match

**Residual Risk**: Very Low (IPFS provides strong guarantees)

---

### T-003: Censorship
**Description**: Attacker attempts to block access to site.

**Impact**: Medium (availability)

**Likelihood**: Varies by jurisdiction

**Mitigations**:
- ✅ IPFS distributed hosting
- ✅ Multiple gateway options
- ✅ Mirroring documentation provided
- ✅ No single point of failure
- ⚠️ Users should pin to multiple nodes
- ⚠️ Consider Tor hidden service

**Residual Risk**: Low-Medium (determined adversaries may succeed locally)

---

### T-004: Gateway Surveillance
**Description**: Public IPFS gateway logs user access.

**Impact**: Medium (privacy)

**Likelihood**: High (gateways may log)

**Mitigations**:
- ⚠️ Documentation warns about gateway privacy
- ⚠️ Recommend self-hosted gateway
- ⚠️ Suggest Tor/VPN usage
- ❌ Cannot prevent gateway logging

**Residual Risk**: Medium (user must take action)

---

### T-005: XSS Injection
**Description**: Attacker injects malicious scripts.

**Impact**: High (code execution)

**Likelihood**: Very Low (static site)

**Mitigations**:
- ✅ Static site (no user input by default)
- ✅ Content Security Policy
- ✅ No inline scripts
- ✅ Input sanitization (if users add forms)

**Residual Risk**: Very Low

---

### T-006: Supply Chain Attack
**Description**: Compromised dependency introduces malicious code.

**Impact**: Critical

**Likelihood**: Low

**Mitigations**:
- ✅ Minimal dependencies
- ✅ Lock files for reproducibility
- ✅ Automated dependency audits (npm audit)
- ✅ GitHub Dependency Review
- ⚠️ Regular updates needed
- ⚠️ Users should audit dependencies

**Residual Risk**: Low-Medium

---

### T-007: Compromised Build Environment
**Description**: Attacker compromises build machine, injects malware.

**Impact**: Critical

**Likelihood**: Low

**Mitigations**:
- ✅ Reproducible builds (Docker)
- ✅ CI/CD automation
- ⚠️ Users must secure build environment
- ⚠️ Verify build outputs

**Residual Risk**: Low (user responsibility)

---

### T-008: Private Key Exposure
**Description**: Deployment credentials or signing keys leaked.

**Impact**: High (unauthorized deployments)

**Likelihood**: Low

**Mitigations**:
- ⚠️ Document secret management
- ⚠️ Use environment variables
- ✅ .gitignore prevents accidental commits
- ⚠️ Rotate keys regularly

**Residual Risk**: Low (user responsibility)

---

### T-009: Clickjacking
**Description**: Site embedded in iframe for phishing.

**Impact**: Medium

**Likelihood**: Low

**Mitigations**:
- ✅ X-Frame-Options: DENY
- ✅ CSP frame-ancestors 'none'

**Residual Risk**: Very Low

---

### T-010: Browser Vulnerabilities
**Description**: Attacker exploits browser bugs.

**Impact**: Varies

**Likelihood**: Low

**Mitigations**:
- ✅ Modern web standards
- ✅ No use of deprecated APIs
- ❌ Cannot patch user's browser

**Residual Risk**: Low (user responsibility)

## Security Controls Summary

| Control | Implemented | Effectiveness |
|---------|-------------|---------------|
| Content Security Policy | ✅ | High |
| No Tracking | ✅ | High |
| Static Site Generation | ✅ | High |
| IPFS Content Addressing | ✅ | High |
| Security Headers | ✅ | Medium-High |
| Dependency Auditing | ✅ | Medium |
| Service Worker (Offline) | ✅ | Medium |
| Reproducible Builds | ✅ | Medium |
| Privacy Documentation | ✅ | Medium |
| Gateway Privacy | ⚠️ | Low (user action needed) |

## Assumptions

1. Users trust their build environment
2. Users will follow security best practices
3. IPFS network remains operational
4. Cryptographic primitives (SHA-256, etc.) remain secure
5. Users verify CIDs before deploying
6. Users keep dependencies updated

## Recommendations for Users

### High Priority
1. ✅ Use self-hosted IPFS gateway for privacy
2. ✅ Pin content to multiple nodes
3. ✅ Run `npm audit` regularly
4. ✅ Verify build output before deployment

### Medium Priority
5. ⚠️ Use Tor or VPN when accessing via public gateways
6. ⚠️ Implement monitoring for availability
7. ⚠️ Consider custom domain with DNSLink
8. ⚠️ Keep build environment secure

### Low Priority
9. ℹ️ Set up mirrors in multiple jurisdictions
10. ℹ️ Document your specific threat model
11. ℹ️ Consider blockchain timestamping for content

## Incident Response

If a security issue is discovered:

1. **DO NOT** open public issue
2. Follow [SECURITY.md](../SECURITY.md) responsible disclosure
3. Contact maintainers privately
4. Allow time for fix before public disclosure

## Review Schedule

This threat model should be reviewed:
- When major changes are made
- At least annually
- After security incidents
- When new threats emerge

---

**Last Reviewed**: 2025-11-12
**Next Review**: 2026-11-12
**Reviewer**: Template Maintainers
