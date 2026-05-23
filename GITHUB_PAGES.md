# GitHub Pages Deployment

This document describes how to deploy the GBA emulator to GitHub Pages.

## Overview

The repository is configured to automatically deploy the WASM GBA emulator to GitHub Pages whenever changes are pushed to the `master` branch.

## Setup Instructions

### 1. Enable GitHub Pages

1. Go to your repository settings on GitHub
2. Navigate to **Settings** → **Pages**
3. Under "Build and deployment":
   - **Source**: Select "GitHub Actions"
   - This allows the workflow to deploy to GitHub Pages

### 2. Workflow Configuration

The deployment workflow is defined in `.github/workflows/deploy-pages.workflow.yml` and will:
- Automatically trigger on pushes to the `master` branch
- Can also be manually triggered via `workflow_dispatch`
- Build the project using the `build:with-coi-serviceworker` script
- Deploy the built files from `gbajs3/dist` to GitHub Pages

### 3. Base Path Configuration

The Vite configuration in `gbajs3/vite.config.ts` automatically sets the correct base path:
- For GitHub Pages: `/gbajs3/` (repository name)
- For local development: `./` (relative path)

The base path is controlled by the `GITHUB_PAGES` environment variable, which is set to `'true'` in the deployment workflow.

## Accessing the Deployed Site

Once deployed, your GBA emulator will be available at:
```
https://[username].github.io/gbajs3/
```

For this repository, it will be:
```
https://cemberk.github.io/gbajs3/
```

## Manual Deployment

You can also manually trigger a deployment:
1. Go to **Actions** tab in your GitHub repository
2. Select the "Deploy to GitHub Pages" workflow
3. Click "Run workflow"
4. Select the `master` branch and click "Run workflow"

## Build Requirements

The deployment process requires:
- Node.js 24.14.0 (as specified in the workflow)
- All dependencies installed via `npm ci`
- Cross-Origin-Isolation headers are handled via the COI service worker

## Troubleshooting

### Deployment Fails

1. Check that GitHub Pages is enabled in repository settings
2. Verify that the workflow has the necessary permissions:
   - `contents: read`
   - `pages: write`
   - `id-token: write`

### Site Not Loading Correctly

1. Ensure the base path in `vite.config.ts` matches your repository name
2. Check browser console for CORS or path-related errors
3. Verify that the COI service worker is being loaded correctly

### WASM Not Working

The emulator requires Cross-Origin-Isolation to use SharedArrayBuffer for WASM threading. The deployment includes a COI service worker (`coi-sw.js`) that sets the required headers:
- `Cross-Origin-Embedder-Policy: require-corp`
- `Cross-Origin-Opener-Policy: same-origin`

If WASM features aren't working, ensure your browser supports these headers and that the service worker is registered correctly.

## Development vs Production

- **Development**: Uses `npm run dev` with relative paths (`./`)
- **Production (GitHub Pages)**: Uses `npm run build:with-coi-serviceworker` with absolute paths (`/gbajs3/`)

The configuration automatically handles both scenarios based on the `GITHUB_PAGES` environment variable.
