# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**MecanicaAlvaro** is a full-stack mechanic shop management system with:
- **Frontend**: React admin dashboard built on Mantis Free Material React template (v1.6.0) with Material UI v7
- **Backend**: Laravel 12 REST API (PHP 8.3)
- **Database**: MySQL 8.4
- **Infrastructure**: Docker Compose with 3 services

**Architecture**: Monorepo structure where frontend and backend can be developed independently but are deployed together.

## Quick Start

### Prerequisites
- Docker Desktop installed and running
- Git

### Starting the Application
```bash
# From project root
docker-compose up -d

# All services will start automatically:
# - Frontend: http://localhost:3030
# - Backend API: http://localhost:80
# - MySQL: localhost:3307
```

### Stopping the Application
```bash
docker-compose down
```

## Project Structure

### Root Organization
```
mechanic-suite/
├── frontend/              # React Dashboard (Mantis Material UI)
│   ├── src/              # React source code
│   ├── Dockerfile        # Frontend container config
│   ├── package.json
│   └── vite.config.mjs
├── backend/
│   ├── site/             # Laravel 12 application
│   │   ├── app/         # Laravel app logic
│   │   ├── database/    # Migrations, seeders, factories
│   │   ├── routes/      # API routes
│   │   └── .env        # Laravel environment config (not in git)
│   ├── docker/
│   │   └── apache/
│   │       └── 000-default.conf  # Apache VirtualHost
│   └── Dockerfile       # Backend container config
├── docker-compose.yml   # Orchestrates all services
├── .gitignore          # Root-level ignores
├── CLAUDE.md           # This file
└── README.md           # User documentation
```

### Frontend Source Structure (`frontend/src/`)
```
src/
├── api/                 # API utilities and menu configuration
├── assets/              # Images, fonts, third-party CSS
├── components/          # Reusable UI components
│   ├── @extended/       # Custom extended components
│   ├── cards/           # Card and statistics components
│   └── logo/            # Logo components
├── layout/              # Layout components
│   ├── Auth/            # Authentication layout
│   └── Dashboard/       # Main dashboard layout
├── pages/               # Route pages (lazy-loaded)
│   ├── auth/            # Login/signup pages
│   ├── dashboard/       # Dashboard pages
│   └── extra-pages/     # Additional pages
├── routes/              # React Router configuration
├── themes/              # MUI theme configuration
├── utils/               # Utility functions
├── App.jsx              # Root component
└── index.jsx            # React DOM entry point
```

### Backend Structure (`backend/site/`)
```
site/
├── app/
│   ├── Http/
│   │   ├── Controllers/    # API controllers
│   │   └── Middleware/
│   ├── Models/             # Eloquent models
│   └── Providers/
├── database/
│   ├── migrations/         # Database migrations
│   ├── seeders/            # Data seeders
│   └── factories/          # Model factories
├── routes/
│   ├── web.php            # Web routes
│   ├── api.php            # API routes (not created yet, use web.php)
│   └── console.php        # Console commands
├── public/                # Apache DocumentRoot
│   └── index.php         # Entry point
├── storage/               # Logs, cache, uploads
│   └── logs/
│       └── laravel.log
├── .env                  # Environment configuration (gitignored)
├── .env.example          # Environment template
├── composer.json         # PHP dependencies
└── artisan              # Laravel CLI
```

## Docker Services

### Service Details

| Service | Image | Port | Description |
|---------|-------|------|-------------|
| `frontend` | Node 20 Alpine | 3030→5173 | React with Vite dev server |
| `backend` | PHP 8.3 Apache | 80 | Laravel API |
| `mysql` | MySQL 8.4 | 3307→3306 | Database |

### Docker Network
- Network name: `mechanic-network` (bridge driver)
- Services communicate using service names (e.g., `mysql`, `backend`, `frontend`)

### Volumes
- `mysql_data` - Persistent MySQL data
- `./frontend:/app` - Frontend code (hot reload)
- `./backend/site:/app/site` - Backend code (hot reload)

## Common Commands

### Docker Commands

```bash
# View all container logs
docker-compose logs -f

# View specific service logs
docker logs mechanic-frontend
docker logs mechanic-backend
docker logs mechanic-mysql

# Restart a specific service
docker-compose restart frontend
docker-compose restart backend

# Rebuild containers
docker-compose up -d --build

# Stop all services
docker-compose down

# Stop and remove volumes (WARNING: deletes database)
docker-compose down -v

# Execute command in container
docker exec -it mechanic-backend sh
docker exec -it mechanic-frontend sh
```

### Frontend Commands

**From host machine (outside container):**
```bash
# These commands run inside the Docker container automatically
docker-compose logs frontend  # View frontend logs
docker-compose restart frontend
```

**Inside container:**
```bash
docker exec -it mechanic-frontend sh

# Then run:
yarn install       # Install dependencies
yarn lint          # Check code quality
yarn lint:fix      # Auto-fix linting issues
yarn prettier      # Format code
yarn build         # Production build
```

**Note**: Vite dev server runs automatically when container starts (`yarn start`)

### Backend Commands

**From host machine:**
```bash
# Access Laravel container
docker exec -it mechanic-backend sh

# Run Artisan commands directly
docker exec mechanic-backend php artisan route:list
docker exec mechanic-backend php artisan migrate
docker exec mechanic-backend php artisan make:controller ExampleController
```

**Inside container:**
```bash
# Artisan CLI
php artisan migrate              # Run migrations
php artisan migrate:fresh        # Drop all tables and re-run migrations
php artisan migrate:rollback     # Rollback last migration
php artisan make:migration create_example_table
php artisan make:model Example
php artisan make:controller ExampleController
php artisan tinker               # Laravel REPL
php artisan route:list           # List all routes

# Composer
composer install                 # Install dependencies
composer require vendor/package  # Add package
composer dump-autoload           # Regenerate autoload files
```

### Database Commands

```bash
# Access MySQL CLI
mysql -h 127.0.0.1 -P 3307 -uroot -proot_password mechanic

# From inside container
docker exec -it mechanic-mysql mysql -uroot -proot_password mechanic

# Backup database
docker exec mechanic-mysql mysqldump -uroot -proot_password mechanic > backup.sql

# Restore database
mysql -h 127.0.0.1 -P 3307 -uroot -proot_password mechanic < backup.sql

# Check database from backend container
docker exec mechanic-backend php artisan db:show
```

## Configuration Files

### Frontend Key Files

**`vite.config.mjs`** - Vite configuration
- Port: **5173** (exposed as 3030 on host)
- Hot Module Replacement enabled
- Path aliasing configured
- Production optimizations (code splitting, console removal)

**`package.json`**
- React 19, MUI v7, React Router v7
- Yarn 4.9.1 (via Corepack)
- Scripts: `start`, `build`, `lint`, `lint:fix`, `prettier`

**`.env` variables** (prefix with `VITE_`):
- `VITE_APP_BASE_NAME` - Base URL path
- `VITE_APP_VERSION` - App version

### Backend Key Files

**`.env`** (in `backend/site/`, gitignored)
```env
APP_NAME="Mecanica Alvaro"
APP_ENV=local
APP_DEBUG=true
APP_KEY=base64:... (generated by Laravel)

DB_CONNECTION=mysql
DB_HOST=mysql          # Docker service name
DB_PORT=3306           # Internal port
DB_DATABASE=mechanic
DB_USERNAME=root
DB_PASSWORD=root_password
```

**`config/database.php`** - Database configuration (uses .env variables)

**`routes/web.php`** - Define API endpoints here
```php
Route::get('/api/example', function () {
    return response()->json(['message' => 'Hello from Laravel']);
});
```

### Docker Files

**`docker-compose.yml`**
- Orchestrates 3 services
- Defines network and volumes
- Environment variables
- Port mappings
- Startup commands

**`backend/Dockerfile`**
- PHP 8.3 with Apache
- Installs Composer and PHP extensions
- Copies Apache config
- Sets file permissions
- **Important**: Permissions are fixed on every container start

**`frontend/Dockerfile`**
- Node 20 Alpine (multi-stage build)
- Enables Corepack for Yarn 4
- Installs dependencies
- Runs Vite dev server

## Architecture & Technical Details

### Frontend Stack

**State Management:**
- React Context API for global state
- SWR (2.3.4) for data fetching with automatic revalidation

**Routing:**
- React Router v7 with BrowserRouter
- Lazy loading via `React.lazy()` and `Loadable` HOC
- Routes: `MainRoutes` (authenticated) and `LoginRoutes` (public)

**UI & Styling:**
- Material UI (MUI) v7 - Component library
- Emotion CSS-in-JS - Styling solution
- Ant Design Icons - Icon library
- Framer Motion - Animations
- Custom theme system in `themes/`

**Forms:**
- Formik - Form state management
- Yup - Validation schemas
- React Number Format - Number formatting

**Build Tool:**
- Vite 7 - Fast builds and HMR
- Code splitting by route
- Asset optimization (images, fonts, css)

### Backend Stack

**Framework:**
- Laravel 12 (PHP 8.3)
- Zero breaking changes from Laravel 11
- Released: February 24, 2025

**Database:**
- MySQL 8.4
- Eloquent ORM for models
- Query Builder for complex queries
- Migrations for schema management

**API:**
- RESTful API design (to be implemented)
- JSON responses
- CORS middleware (configure in `config/cors.php`)
- Authentication (not configured yet - use Laravel Sanctum or Passport)

**Server:**
- Apache 2.4
- PHP-FPM not used (using mod_php)
- `mod_rewrite` enabled for pretty URLs

### Docker Orchestration

**Startup Sequence:**
1. MySQL starts and initializes database
2. Backend starts, waits for MySQL, runs migrations
3. Frontend starts and connects to backend

**Auto-configuration:**
- Backend: Fixes permissions → runs composer install → runs migrations → starts Apache
- Frontend: Installs yarn dependencies → starts Vite dev server
- MySQL: Initializes with root password and mechanic database

**Networking:**
- All services on `mechanic-network` bridge
- Internal DNS: services access each other by name
- External access: via port mappings

## Development Workflow

### Making Changes

**Frontend:**
1. Edit files in `frontend/src/`
2. Changes hot-reload automatically (Vite HMR)
3. Check browser console for errors
4. Run `yarn lint:fix` before committing

**Backend:**
1. Edit files in `backend/site/`
2. Changes reflect immediately (volume mount)
3. For new migrations: `docker exec mechanic-backend php artisan migrate`
4. Check logs: `docker logs mechanic-backend`

**Database:**
1. Create migration: `docker exec mechanic-backend php artisan make:migration create_table_name`
2. Edit migration file in `backend/site/database/migrations/`
3. Run: `docker exec mechanic-backend php artisan migrate`

### API Development

**Creating an Endpoint:**
1. Add route in `backend/site/routes/web.php`:
```php
Route::get('/api/vehicles', [VehicleController::class, 'index']);
```

2. Create controller:
```bash
docker exec mechanic-backend php artisan make:controller VehicleController
```

3. Implement logic in `app/Http/Controllers/VehicleController.php`

4. Create model:
```bash
docker exec mechanic-backend php artisan make:model Vehicle -m
```

5. Test endpoint: `curl http://localhost/api/vehicles`

**Consuming from Frontend:**
```javascript
import useSWR from 'swr';

function Vehicles() {
  const { data, error } = useSWR('http://localhost/api/vehicles', fetcher);

  if (error) return <div>Error loading</div>;
  if (!data) return <div>Loading...</div>;

  return <div>{JSON.stringify(data)}</div>;
}
```

### Database Seeding

```bash
# Create seeder
docker exec mechanic-backend php artisan make:seeder VehicleSeeder

# Edit backend/site/database/seeders/VehicleSeeder.php

# Run seeder
docker exec mechanic-backend php artisan db:seed --class=VehicleSeeder

# Run all seeders
docker exec mechanic-backend php artisan db:seed
```

## Troubleshooting

### Frontend Issues

**Port 3030 shows "Connection refused":**
```bash
# Check if container is running
docker ps | grep mechanic-frontend

# Check logs
docker logs mechanic-frontend

# Restart container
docker-compose restart frontend
```

**Vite errors about dependencies:**
```bash
# Reinstall dependencies
docker exec mechanic-frontend rm -rf node_modules
docker-compose restart frontend
```

### Backend Issues

**500 Internal Server Error:**
```bash
# Check Laravel logs
docker exec mechanic-backend tail -f /app/site/storage/logs/laravel.log

# Check Apache logs
docker exec mechanic-backend tail -f /var/log/apache2/error.log

# Check file permissions
docker exec mechanic-backend ls -la /app/site/storage
```

**"Storage not writable" errors:**
```bash
# Fix permissions (runs automatically on container start, but can run manually)
docker exec mechanic-backend chown -R www-data:www-data /app/site/storage /app/site/bootstrap/cache
docker exec mechanic-backend chmod -R 775 /app/site/storage /app/site/bootstrap/cache
```

**Database connection errors:**
```bash
# Verify MySQL is running
docker ps | grep mechanic-mysql

# Test connection from backend
docker exec mechanic-backend php artisan db:show

# Check .env file has correct DB_HOST=mysql
docker exec mechanic-backend cat /app/site/.env | grep DB_
```

### MySQL Issues

**Can't connect from GUI client:**
- Use host: `127.0.0.1` (not `localhost`)
- Port: `3307`
- Username: `root`
- Password: `root_password`
- Database: `mechanic`
- Ensure your client supports `caching_sha2_password` (MySQL 8.4 default)

**Database not persisting:**
```bash
# Check if volume exists
docker volume ls | grep mechanic

# Data is stored in: mechanic-suite_mysql_data
```

### General Docker Issues

**Services won't start:**
```bash
# Check Docker Desktop is running
# Check ports aren't already in use
netstat -an | grep "3030\|3307\|80"

# Rebuild everything
docker-compose down
docker-compose up -d --build
```

**"Port already allocated" errors:**
```bash
# Find what's using the port
netstat -ano | grep :<port>

# Kill the process or change port in docker-compose.yml
```

## Important Notes

### File Permissions (Windows/WSL)
- Backend storage and cache permissions are fixed automatically on every container start
- This is necessary because Windows file permissions don't map directly to Linux

### Environment Files
- `backend/site/.env` is gitignored and contains sensitive data
- Use `.env.example` as a template for new environments
- **Never commit `.env` files to git**

### Database Migrations
- Run automatically on container start via docker-compose command
- For manual migration: `docker exec mechanic-backend php artisan migrate`
- To rollback: `docker exec mechanic-backend php artisan migrate:rollback`

### Hot Reloading
- **Frontend**: Full hot module replacement (instant updates)
- **Backend**: File changes apply immediately (volume mount), but may need to clear cache
- **Database**: Changes require new migration or manual SQL

### Git Structure
- Monorepo: single repository with frontend and backend
- Each project has its own `.gitignore`
- Root `.gitignore` handles Docker and IDE files
- Projects can be separated into individual repos later if needed

### Production Considerations
- Change `APP_ENV` to `production` in backend `.env`
- Set `APP_DEBUG=false`
- Generate new `APP_KEY` for production
- Use proper MySQL user (not root)
- Enable HTTPS (add SSL certificates to Apache config)
- Build frontend with `yarn build` and serve static files
- Use environment-specific `.env` files

## URLs & Access

### Development URLs
- **Frontend Dashboard**: http://localhost:3030
- **Backend API**: http://localhost:80 or http://localhost/api/*
- **MySQL**: localhost:3307

### Default Credentials
**MySQL:**
- Username: `root`
- Password: `root_password`
- Database: `mechanic`

## Additional Resources

- [README.md](./README.md) - User-facing documentation with setup instructions
- [Laravel 12 Docs](https://laravel.com/docs/12.x) - Official Laravel documentation
- [React Router Docs](https://reactrouter.com/) - React Router v7 documentation
- [Mantis Template Docs](https://mantisdashboard.io/free) - Dashboard template documentation
- [Vite Docs](https://vite.dev) - Vite build tool documentation
