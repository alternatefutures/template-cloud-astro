# Architecture Decision Records (ADR)

This directory contains Architecture Decision Records for this project.

## What is an ADR?

An Architecture Decision Record (ADR) captures important architectural decisions made during the project's lifetime, along with their context and consequences.

## Format

Each ADR should contain:
- **Title**: Short descriptive title
- **Status**: Proposed, Accepted, Deprecated, Superseded
- **Context**: What is the issue we're trying to solve?
- **Decision**: What is the decision we made?
- **Consequences**: What becomes easier or harder as a result?

## Index

- [ADR-001](001-static-site-generation.md) - Use Static Site Generation
- [ADR-002](002-ipfs-deployment.md) - Deploy on IPFS
- [ADR-003](003-no-tracking.md) - No Analytics or Tracking
- [ADR-004](004-gpl-3-license.md) - Use GPL-3.0 License
- [ADR-005](005-content-security-policy.md) - Implement Strict CSP

## Creating New ADRs

1. Copy template: `cp docs/adr/template.md docs/adr/00X-title.md`
2. Fill in the sections
3. Update this index
4. Submit PR for discussion
