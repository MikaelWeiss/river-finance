# AGENTS.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

River Finance is a personal finance tool focused on simplifying budgeting through a single-screen interface. The application is built using Tauri v2 with a SvelteKit frontend and Rust backend, targeting cross-platform desktop deployment.

## Architecture

### Technology Stack
- **Frontend**: SvelteKit 2.9 + TypeScript 5.6 + Vite 6
- **Backend**: Rust with Tauri v2 framework
- **Package Manager**: pnpm (required for development)
- **Build System**: Tauri CLI + Vite

### Project Structure
```
├── src/                    # SvelteKit frontend source
│   ├── routes/            # SvelteKit routing
│   ├── app.html          # Main HTML template
├── src-tauri/            # Rust backend source
│   ├── src/
│   │   ├── main.rs       # Application entry point
│   │   └── lib.rs        # Main application logic and Tauri commands
│   ├── Cargo.toml        # Rust dependencies
│   └── tauri.conf.json   # Tauri configuration
├── static/               # Static assets
└── package.json          # Node.js dependencies and scripts
```

### Frontend Architecture
- **SPA Mode**: Configured as Single Page Application using `@sveltejs/adapter-static`
- **No SSR**: Server-side rendering is disabled (`ssr = false`)
- **Svelte 5**: Uses the latest Svelte version with runes ($state, etc.)
- **Development Server**: Runs on localhost:1420 with HMR on port 1421

### Backend Architecture
- **Tauri Commands**: Rust functions exposed to frontend via `#[tauri::command]`
- **Plugin System**: Uses `tauri-plugin-opener` for system operations
- **Library Structure**: Main logic in `lib.rs` with binary wrapper in `main.rs`

## Development Commands

### Core Development
```bash
# Start development server (both frontend and Tauri)
pnpm tauri dev

# Build for production
pnpm tauri build

# Frontend-only development (without Tauri)
pnpm dev

# Build frontend only
pnpm build

# Preview production build
pnpm preview
```

### Code Quality
```bash
# Type checking and validation
pnpm check

# Continuous type checking
pnpm check:watch

# Run Rust clippy (lint)
cd src-tauri && cargo clippy

# Run Rust tests
cd src-tauri && cargo test

# Format Rust code
cd src-tauri && cargo fmt
```

### Tauri-Specific Commands
```bash
# Generate Tauri icons from source
pnpm tauri icon [source-icon-path]

# Add Tauri plugin
pnpm tauri add [plugin-name]

# Build for specific target
pnpm tauri build --target [target]

# Create app bundle without building
pnpm tauri bundle
```

## Key Configuration Files

- **`tauri.conf.json`**: Main Tauri configuration including window settings, security, and build commands
- **`vite.config.js`**: Frontend build configuration with Tauri-specific optimizations
- **`svelte.config.js`**: SvelteKit configuration for static adapter
- **`Cargo.toml`**: Rust dependencies and library configuration

## Development Workflow

1. **Frontend Changes**: Edit files in `src/` - changes are hot-reloaded in dev mode
2. **Backend Changes**: Edit files in `src-tauri/src/` - requires restart of `pnpm tauri dev`
3. **Adding Tauri Commands**: Add functions to `src-tauri/src/lib.rs` and register in `invoke_handler`
4. **Frontend-Backend Communication**: Use `invoke()` from `@tauri-apps/api/core` to call Rust commands

## Important Development Notes

- **Fixed Ports**: Development server must run on port 1420 (configured in Vite)
- **Package Manager**: Must use `pnpm` (lockfile is `pnpm-lock.yaml`)
- **Window Configuration**: Default window is 800x600, configurable in `tauri.conf.json`
- **Security**: CSP is disabled (`"csp": null`) for development convenience
- **Asset Handling**: Static assets go in `static/` directory, accessible at root path
- **Tauri Context**: All frontend routes run in Tauri's webview context, not a browser

## Building and Distribution

- **Development Bundle**: `pnpm tauri build` creates platform-specific installers
- **Icons**: Multiple icon sizes in `src-tauri/icons/` for different platforms
- **Targets**: Configured to build for "all" platforms in `tauri.conf.json`
- **App Identifier**: `org.weisssolutions.riverfinance` for app store distribution

