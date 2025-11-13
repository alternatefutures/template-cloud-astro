# Contributing to Astro + Alternate Futures Starter Kit

Thank you for your interest in contributing to this project! We welcome contributions from the community that align with our values of open source, privacy, and censorship resistance.

## Code of Conduct

This project adheres to a Code of Conduct that all contributors are expected to follow. Please read [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) before contributing.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When creating a bug report, include:

- A clear and descriptive title
- Steps to reproduce the issue
- Expected behavior vs actual behavior
- Screenshots (if applicable)
- Your environment (OS, Node version, browser, etc.)
- Any relevant logs or error messages

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

- Use a clear and descriptive title
- Provide a detailed description of the proposed feature
- Explain why this enhancement aligns with our privacy and censorship-resistance values
- Include examples of how the feature would be used

### Security Vulnerabilities

**DO NOT** open public issues for security vulnerabilities. Please follow our [Security Policy](SECURITY.md) for responsible disclosure.

## Development Process

### Setting Up Your Development Environment

1. Fork the repository
2. Clone your fork: `git clone https://github.com/YOUR-USERNAME/template-cloud-astro.git`
3. Install dependencies: `npm install` or `pnpm install`
4. Create a branch: `git checkout -b feature/your-feature-name`

### Development Guidelines

#### Code Style

- We use ESLint and Prettier for code formatting
- Run `npm run lint` before committing
- Follow existing code patterns and conventions
- Write clear, self-documenting code with comments where necessary

#### Privacy & Security First

- **Never** introduce analytics, tracking, or telemetry
- **Never** add external dependencies that phone home
- **Never** include third-party CDNs or external resources without discussion
- Audit all new dependencies for privacy implications
- Prefer self-hosted resources over external ones

#### Accessibility

- Ensure all UI changes meet WCAG 2.1 AA standards
- Include proper ARIA labels and semantic HTML
- Test with keyboard navigation
- Verify color contrast ratios

#### Censorship Resistance

- Maintain static site generation (no server dependencies)
- Ensure IPFS compatibility
- Keep the build output portable and self-contained
- Document any new deployment requirements

### Testing

- Test your changes locally with `npm run dev`
- Build and preview production output: `npm run build && npm run preview`
- Verify IPFS deployment compatibility
- Test in multiple browsers if making UI changes

### Commit Messages

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

- `feat:` New features
- `fix:` Bug fixes
- `docs:` Documentation changes
- `style:` Code style changes (formatting, etc.)
- `refactor:` Code refactoring
- `test:` Adding or updating tests
- `chore:` Maintenance tasks

Examples:
```
feat: add PWA manifest for offline support
fix: resolve CSP violation in Card component
docs: update IPFS deployment instructions
```

### Pull Request Process

1. Update the README.md or relevant documentation with details of changes
2. Update the CHANGELOG.md following [Keep a Changelog](https://keepachangelog.com/) format
3. Ensure all tests pass and the build succeeds
4. Request review from maintainers
5. Address any feedback from code review

#### Pull Request Checklist

- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated (if applicable)
- [ ] No new warnings or errors introduced
- [ ] CHANGELOG.md updated
- [ ] No privacy or security concerns introduced
- [ ] Tested locally and in production build
- [ ] IPFS deployment verified (if applicable)

## Dependency Management

### Adding New Dependencies

New dependencies must be carefully evaluated:

1. **Necessity**: Is this dependency truly needed?
2. **Licensing**: Is it compatible with GPL-3.0?
3. **Privacy**: Does it track users or phone home?
4. **Maintenance**: Is it actively maintained?
5. **Size**: What's the bundle size impact?
6. **Alternatives**: Are there lighter/more private alternatives?

Open an issue to discuss new dependencies before submitting a PR.

### Updating Dependencies

- Keep dependencies up to date for security
- Test thoroughly after updates
- Document any breaking changes in CHANGELOG.md

## Documentation

- Use clear, concise language
- Include code examples where helpful
- Keep documentation up to date with code changes
- Consider non-technical users when writing guides

## Community

### Getting Help

- Check existing documentation and issues first
- Ask questions in GitHub Discussions (if enabled)
- Be respectful and patient

### Recognition

Contributors are recognized in our project. Significant contributors may be listed in a CONTRIBUTORS.md file.

## License

By contributing to this project, you agree that your contributions will be licensed under the GPL-3.0 License. See [LICENSE](LICENSE) for details.

## Questions?

If you have questions about contributing, feel free to open an issue labeled "question" or reach out to the maintainers.

---

Thank you for helping make the web more open, private, and censorship-resistant!
