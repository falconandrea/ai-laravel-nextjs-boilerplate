# Tech Stack - Laravel Project

> **CRITICAL**: AI must use these EXACT versions. Never deviate without explicit permission.

## Backend Stack

### Core Framework
- **Laravel**: ^12.0 (latest stable)
- **PHP**: ^8.3
- **Composer**: ^2.7

### Development Environment
- **Laravel Sail**: Latest (Docker-based local development)
  - MySQL 8.0
  - Redis 7.0
  - Mailpit (for local email testing)
  - Meilisearch (optional - for search)

### Database
- **MySQL**: 8.0 (via Sail)
- **Migration Tool**: Laravel Migrations
- **Seeding**: Laravel Seeders + Factories

### Authentication & Authorization
- **Laravel Breeze**: ^2.0 (or specify Sanctum/Passport if API)
- **Session Driver**: database (or redis for production)

### Frontend (if using Blade/Inertia)
- **Vite**: ^5.0 (asset bundling)
- **TailwindCSS**: ^3.4.0
- **Alpine.js**: ^3.13 (optional, for Blade interactivity)

### Testing
- **PHPUnit**: ^11.0 (comes with Laravel)
- **Pest**: ^3.0 (optional but recommended)

### Code Quality
- **Laravel Pint**: Latest (code formatting)
- **PHPStan**: ^1.10 (static analysis - optional)
- **Larastan**: ^2.9 (Laravel-specific PHPStan rules)

## Production Environment

### Hosting
- VPS with Docker and traefik

### Database
- Container with MySQL 8.0 or MariaDB 10.11

### Cache & Queue
- **Cache Driver**: redis
- **Queue Driver**: redis (or database for simple projects)
- **Queue Worker**: Supervisor (for process management)

### Storage
- **File Storage**: local
- **Driver**: Laravel Filesystem (Flysystem)

### Email
- **Development**: Mailpit (via Sail)
- **Production**: SMTP service

### Monitoring & Logging
- **Logging**: Laravel Log (daily rotation)
- **Error Tracking**: Sentry
- **APM**: None

## Development Commands

### Sail Commands (Always use these in local dev)
```bash
# Start environment
./vendor/bin/sail up -d

# Run artisan commands
./vendor/bin/sail artisan [command]

# Run composer
./vendor/bin/sail composer [command]

# Run tests
./vendor/bin/sail test

# Access MySQL
./vendor/bin/sail mysql

# Access Redis CLI
./vendor/bin/sail redis
```

## Package Constraints

### Allowed Packages (pre-approved)
- `spatie/laravel-permission` - Role & Permission management
- `spatie/laravel-query-builder` - API filtering/sorting
- `spatie/laravel-data` - DTOs
- `spatie/laravel-medialibrary` - File uploads

### Forbidden Patterns
- ❌ NO raw DB queries without Eloquent (use Query Builder minimum)
- ❌ NO `eval()` or `exec()` functions
- ❌ NO deprecated Laravel helpers (check Laravel 12 docs)
- ❌ NO mixing Eloquent and Query Builder in same query

## Version Lock Justification

**Why Laravel 12?**
- Latest stable with [list key features needed]

**Why PHP 8.3?**
- Required for Laravel 12
- Performance improvements
- Typed properties

**Why Sail?**
- Consistent dev environment across team
- Zero Docker knowledge required
- Includes all services pre-configured

## Notes
- Update this file if ANY dependency changes
- Document version upgrade decisions in `ARCHITECTURE_DECISIONS.md`
- AI: NEVER suggest packages not listed here without asking first
