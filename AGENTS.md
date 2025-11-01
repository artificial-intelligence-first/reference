---
title: AGENTS
slug: agents
status: living
last_updated: 2025-11-01
tags: [agents, workflow, ai-first]
summary: "AI agent operational documentation providing machine-focused setup, test, and deployment instructions."
authors: []
sources:
  - { id: R1, title: "AGENTS.md Official Website", url: "https://agents.md", accessed: "2025-10-23" }
  - { id: R2, title: "Cursor - AI-first Code Editor", url: "https://cursor.com", accessed: "2025-10-23" }
  - { id: R3, title: "DeepWiki: openai/agents.md Documentation", url: "https://deepwiki.com/openai/agents.md", accessed: "2025-10-23" }
---

# AGENTS

> **For Humans**: This document provides AI-agent-friendly operational documentation for your repository. It helps AI coding assistants understand your project setup, workflows, and conventions.
>
> **For AI Agents**: Read this first to understand project setup, development environment, testing procedures, and operational workflows. Follow these instructions when working on this codebase.

## Dev Environment

### Prerequisites

```bash
# Node.js 18+ required
node --version  # Should output v18.0.0 or higher

# Package manager
npm --version   # 8.0.0+
# OR
pnpm --version  # 8.0.0+
```

### Initial Setup

```bash
# 1. Clone repository
git clone <repository-url>
cd <repository-name>

# 2. Install dependencies
npm install
# OR
pnpm install

# 3. Copy environment variables
cp .env.example .env

# 4. Verify setup
npm run verify
```

### Environment Variables

Required variables in `.env`:
```bash
NODE_ENV=development|production|test
LOG_LEVEL=debug|info|warn|error
API_KEY=<your-api-key>
DATABASE_URL=<connection-string>
```

## Test & Lint

### Running Tests

```bash
# Unit tests
npm run test

# Integration tests
npm run test:integration

# E2E tests
npm run test:e2e

# Coverage report
npm run test:coverage

# Watch mode
npm run test:watch
```

### Linting

```bash
# Lint code
npm run lint

# Auto-fix issues
npm run lint:fix

# Type checking
npm run type-check

# Format code
npm run format
```

### Pre-commit Checks

```bash
# Run all checks
npm run pre-commit

# Individual checks
npm run lint:staged
npm run test:staged
```

## Build & Deploy

### Build Process

```bash
# Development build
npm run build:dev

# Production build
npm run build

# Build with analysis
npm run build:analyze
```

### Deployment

```bash
# Deploy to staging
npm run deploy:staging

# Deploy to production
npm run deploy:production

# Rollback
npm run deploy:rollback
```

### Docker

```dockerfile
# Build image
docker build -t app:latest .

# Run container
docker run -p 3000:3000 app:latest

# Compose
docker-compose up -d
```

## PR/Commit Rules

### Branch Naming

```
feature/<ticket>-<description>
bugfix/<ticket>-<description>
hotfix/<ticket>-<description>
chore/<description>
docs/<description>
```

### Commit Format

```
<type>(<scope>): <description>

[optional body]
[optional footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### PR Checklist

- [ ] Tests pass locally
- [ ] Lint passes
- [ ] Documentation updated
- [ ] Changelog entry added
- [ ] No console.log statements
- [ ] No commented code
- [ ] Branch up-to-date with main

### Review Process

1. Create PR with description
2. Automated checks must pass
3. Request review from codeowners
4. Address feedback
5. Squash and merge

## Security

### Secrets Management

```bash
# Never commit secrets
# Use environment variables
# Rotate keys regularly

# Scan for secrets
npm run security:secrets

# Update dependencies
npm run security:audit
npm run security:fix
```

### Security Headers

Required headers:
- `Content-Security-Policy`
- `X-Frame-Options`
- `X-Content-Type-Options`
- `Strict-Transport-Security`

### Vulnerability Scanning

```bash
# Dependency audit
npm audit

# Container scanning
docker scan app:latest

# SAST analysis
npm run security:sast
```

## Common Tasks

### Database Operations

```bash
# Run migrations
npm run db:migrate

# Rollback migration
npm run db:rollback

# Seed database
npm run db:seed

# Reset database
npm run db:reset
```

### Cache Management

```bash
# Clear all caches
npm run cache:clear

# Clear specific cache
npm run cache:clear -- --type=redis

# Warm cache
npm run cache:warm
```

### Monitoring

```bash
# Check health
curl http://localhost:3000/health

# View metrics
curl http://localhost:3000/metrics

# Check logs
npm run logs:tail
```

## Troubleshooting

### Common Issues

**Port already in use**
```bash
# Find process
lsof -i :3000
# Kill process
kill -9 <PID>
```

**Module not found**
```bash
# Clear node_modules
rm -rf node_modules package-lock.json
npm install
```

**Build fails**
```bash
# Clear build cache
npm run clean
npm run build
```

### Debug Mode

```bash
# Enable debug logging
DEBUG=* npm run dev

# Verbose output
npm run dev -- --verbose

# Inspector mode
node --inspect npm run dev
```

## Performance

### Optimization Checklist

- [ ] Bundle size < 200KB (gzipped)
- [ ] First paint < 1.5s
- [ ] TTI < 3.5s
- [ ] Lighthouse score > 90
- [ ] No memory leaks
- [ ] Database queries optimized

### Profiling

```bash
# CPU profiling
npm run profile:cpu

# Memory profiling
npm run profile:memory

# Bundle analysis
npm run analyze
```

## See Also

- [SSOT.md](./SSOT.md) - Single Source of Truth principles
- [AGENTS Guide](./docs/core/agents-guide.md) - Comprehensive AGENTS.md implementation guide
- [CONTRIBUTING.md](./docs/governance/contributing.md) - Contribution guidelines
- [Architecture](./docs/architecture.md) - System architecture

## References

- [R1] AGENTS.md Official Website. https://agents.md (accessed 2025-10-23)
- [R2] Cursor - AI-first Code Editor. https://cursor.com (accessed 2025-10-23)
- [R3] DeepWiki: openai/agents.md Documentation. https://deepwiki.com/openai/agents.md (accessed 2025-10-23)