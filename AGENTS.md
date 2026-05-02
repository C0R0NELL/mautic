# AGENTS.md

This file provides guidance to agents when working with code in this repository.

## Project Overview

Mautic is an open-source marketing automation platform built on **Symfony 7.4** and **PHP 8.2+**. It follows a bundle-based architecture with core functionality in `app/bundles/`, plugins in `plugins/`, and themes in `themes/`.

## Essential Commands

### Development Setup
```bash
# Canonical local setup
ddev start

# Fallback manual setup
composer install
npm ci && npm run build
bin/console mautic:install <site-url>
```

### Testing
```bash
# Run all PHPUnit tests
ddev exec composer test

# Run specific test file
ddev exec bin/phpunit app/bundles/EmailBundle/Tests/Functional/EmailClickTrackingTest.php

# Run specific bundle tests
ddev exec bin/phpunit app/bundles/CoreBundle/Tests

# Run specific test method
ddev exec bin/phpunit --filter testGuessTimezoneFromOffset

# E2E acceptance tests (Codeception)
ddev exec composer run e2e-test
```

### Code Quality
```bash
ddev exec composer phpstan          # Static analysis (level 6)
ddev exec composer cs               # Check coding standards (dry-run)
ddev exec composer fixcs            # Auto-fix coding standards
ddev exec composer rector           # Run Rector refactoring (code + tests)
ddev exec bin/console lint:twig app plugins  # Lint Twig templates
```

### Asset Management
```bash
ddev exec composer generate-assets  # Regenerate compiled assets
ddev exec npm run build             # Build frontend with webpack
```

## Architecture

### Bundle Structure
Each bundle follows this pattern:
```
MauticExampleBundle/
├── Command/          # Console commands
├── Config/           # Bundle config (services in config.php)
├── Controller/       # Controllers (extend CommonController)
├── Entity/           # Doctrine ORM entities + repositories
├── Event/            # Event objects
├── EventListener/    # Event subscribers
├── Form/             # Form types
├── Model/            # Service layer (extend AbstractCommonModel)
├── Tests/            # Unit + Functional tests
│   ├── Unit/
│   └── Functional/
└── Views/            # Twig templates
```

### Key Core Bundles
- **CoreBundle**: Foundation (helpers, security, base classes)
- **LeadBundle**: Contact/company management
- **CampaignBundle**: Campaign builder & execution
- **EmailBundle**: Email marketing
- **FormBundle**: Form builder
- **IntegrationsBundle**: Third-party integrations framework

### Testing Base Classes
- **Unit tests**: Extend `PHPUnit\Framework\TestCase`
- **Functional tests**: Extend `Mautic\CoreBundle\Test\MauticMysqlTestCase` (provides `$this->em`, `$this->client`, database access)

## Coding Standards

- **PHP 8.2+** with strict types, PSR-12/Symfony style
- 4-space indent, short array syntax `[]`, ordered imports
- Explicit types and nullable hints; minimal DocBlocks
- Test naming: `*Test.php`, matching class/feature under test
- Run `composer cs` before PRs; CI will fail otherwise

## Platform Requirements (Mautic 7.0)

- PHP: **8.2+**
- MySQL: **8.4.0+** or MariaDB: **10.11.0+**
- Removed: Browser-based updates (use CLI only)
- Removed: MauticFactory (use dependency injection)

## PR Checklist

Before submitting PRs, run:
```bash
ddev exec composer test      # PHPUnit tests pass
ddev exec composer phpstan   # Static analysis passes
ddev exec composer cs        # Coding standards check
```

For UI changes, also run `ddev exec composer run e2e-test`.

## Configuration

- Local config: `config/local.php` (gitignored)
- Environment configs: `app/config/config_*.php`
- Test environment: `APP_ENV=test`
- DDEV auto-generates `config/local.php` from `.ddev/local.config.php.dist`
- Canonical local flow in this workspace: `DDEV`.
