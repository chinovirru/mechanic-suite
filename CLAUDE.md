# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**MecanicaAlvaro** is a React admin dashboard application built on the Mantis Free Material React template (v1.6.0). It uses Material UI (MUI v7) with Ant Design principles.

**Note:** This is a frontend-only project. The `backend/` directory exists but is empty.

## Common Commands

All commands should be run from the `frontend/` directory:

### Development & Build
```bash
cd frontend

yarn start          # Start dev server (port 3000, auto-opens browser)
yarn build          # Production build
yarn preview        # Preview production build

# Code Quality
yarn lint           # Check for code quality issues
yarn lint:fix       # Auto-fix linting issues
yarn prettier       # Format code with Prettier
```

## Project Structure

### Root Organization
- `frontend/` - Complete React application (JSX, components, layouts, pages)
- `backend/` - Empty (no backend implementation)

### Frontend Source Structure
```
src/
├── api/                 # API utilities and menu configuration
├── assets/              # Images, fonts, third-party CSS (ApexCharts, React Table)
├── components/          # Reusable UI components
│   ├── @extended/       # Custom extended components
│   ├── cards/           # Card and statistics components
│   └── logo/            # Logo components
├── contexts/            # React Context API preparation (empty README)
├── data/                # Static/mock data
├── hooks/               # Custom React hooks (empty README)
├── layout/              # Layout components
│   ├── Auth/            # Authentication layout
│   └── Dashboard/       # Main dashboard layout
│       ├── Drawer/      # Sidebar navigation drawer
│       ├── Header/      # Top navigation header
│       └── Footer/
├── menu-items/          # Navigation menu configuration
├── pages/               # Route pages (lazy-loaded)
│   ├── auth/            # Login/signup pages
│   ├── component-overview/  # Template demo pages
│   ├── dashboard/       # Dashboard pages
│   └── extra-pages/     # Additional sample pages
├── routes/              # React Router configuration
├── sections/            # Complex feature components organized by domain
│   ├── auth/
│   └── dashboard/
├── themes/              # MUI theme configuration
│   ├── overrides/       # MUI component overrides
│   └── theme/           # Theme color and typography definitions
├── utils/               # Utility functions
├── App.jsx              # Root component
├── index.jsx            # React DOM entry point
└── config.js            # App configuration constants
```

### Key Configuration Files
- `vite.config.mjs` - Vite bundler config (port 3000, code splitting, console removal in prod)
- `jsconfig.json` - Path aliasing (imports from `src/`)
- `.env` - Environment variables (VITE_APP_VERSION, VITE_APP_BASE_NAME, etc.)
- `eslint.config.mjs` - ESLint rules (React, Hooks, a11y, Prettier integration)
- `.prettierrc` - Code formatting (single quotes, 2-space indent, 140 char line width)
- `.yarnrc.yml` - Yarn package manager config

## Architecture & Key Patterns

### State Management
- **React Context API** - Global state setup (contexts folder available for expansion)
- **SWR (2.3.4)** - Client-side data fetching with automatic revalidation and caching

### Routing & Code Splitting
- **React Router v7** - Client-side routing with `BrowserRouter`
- **Lazy Loading** - All pages are lazily loaded via `React.lazy()` and a `Loadable` HOC
- Two main route groups: `MainRoutes` (authenticated) and `LoginRoutes` (public)
- Route configuration in `routes/MainRoutes.jsx` and `routes/LoginRoutes.jsx`

### UI Components & Styling
- **Material UI (MUI) v7** - Component library
- **Emotion CSS-in-JS** - Styling solution with MUI integration
- **Ant Design Icons** - Icon library
- **Framer Motion** - Animation library
- MUI theme system with custom overrides in `themes/overrides/`
- Typography fonts: Roboto, Inter, Poppins, Public Sans

### Forms
- **Formik** - Form state management
- **Yup** - Form validation schema
- **React Number Format** - Number input formatting

### Entry Point Flow
1. `index.jsx` - Renders React app with global styles
2. `App.jsx` - Wraps with ThemeCustomization provider and ScrollTop HOC
3. `routes/index.jsx` - Sets up RouterProvider with MainRoutes and LoginRoutes
4. `routes/MainRoutes.jsx` - Authenticates routes under Dashboard layout
5. Pages are lazy-loaded on demand

## Development Notes

### Important Constants
- `APP_DEFAULT_PATH = '/dashboard/default'` - Default route after login
- `DRAWER_WIDTH = 260px` - Sidebar width (normal)
- `MINI_DRAWER_WIDTH = 60px` - Sidebar width (collapsed)

### Vite Configuration Highlights
- **Base URL:** Set via `VITE_APP_BASE_NAME` env variable (default `/`)
- **Port:** 3000 with auto-open enabled
- **Asset Organization:** Automatic grouping into `js/`, `css/`, `images/`, `fonts/`
- **Production Optimizations:** Console/debugger removal, source maps, chunk size warnings
- **Path Aliasing:** Use `import from 'components/...'` syntax (configured via jsconfig.json)

### Code Quality Standards
- **ESLint:** Enforces React, Hooks, and a11y best practices
- **Prettier:** 2-space indentation, single quotes, 140 char line width
- Run `yarn lint:fix` and `yarn prettier` before committing
- Use `yarn lint` to check for issues without auto-fixing

### Main Card Wrapper
- `MainCard` component provides consistent card styling and layout
- Used throughout the dashboard for sections and content

### API Integration Points
- API utilities available in `api/` folder
- SWR is configured for client-side data fetching
- Mock data available in `data/` folder for development

## Template Notes

This is a professional admin dashboard template with demo content. Current pages (Typography, Color, Shadows, Sample Page) are template demonstrations meant to be replaced with actual application features.

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)
- Modern browsers with ES2020+ support

## Build & Deploy Notes

- Production build removes console/debugger statements
- Source maps are generated for debugging (configurable via GENERATE_SOURCEMAP env)
- CSS is code-split automatically
- Output is organized into `js/`, `css/`, `images/`, `fonts/` directories
