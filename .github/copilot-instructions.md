# Copilot Instructions

## Inspira UI Components

When exploring or using Inspira UI components, refer to the official documentation:
**https://inspira-ui.com/docs/components**

Browse the components on that page to find suitable effects and UI elements for this portfolio.

## Testing Changes

Whenever you make changes to this project, follow these steps to verify the changes work correctly:

### 1. Start the Development Server (IMPORTANT - DO THIS FIRST)

Always start the development server in the background before making any changes:

```bash
npm run dev
```

Run this as a background process so the server stays running and you can see live updates via HMR (Hot Module Replacement).

### 2. Make Your Changes

Edit the necessary files. The dev server will automatically hot-reload changes.

### 3. Preview the Changes

Open the local development URL (typically `http://localhost:5173/siri-architect/`) in the browser to visually confirm the effect is successfully applied.

### 4. Build Verification

Once the changes are confirmed working in the dev server, run the build command to ensure the code compiles without errors:

```bash
npm run build
```

Only commit and push changes after both steps pass successfully.

## Project Overview

This is an architecture portfolio website built with:
- **Vue 3** with Composition API (`<script setup>`)
- **Vite** as the build tool
- **Tailwind CSS v4** for styling
- **Inspira UI** components for visual effects

## Key Components

- `AuroraBackground.vue` - Animated aurora background effect
- `FluidCursor.vue` - WebGL fluid simulation cursor effect
- `DirectionAwareHover.vue` - Cards with direction-aware hover animations
- `SparklesText.vue` - Text with animated sparkle effects
- `InteractiveHoverButton.vue` - Button with hover slide animation

## Deployment

The project is deployed to GitHub Pages via GitHub Actions. Any push to `main` triggers automatic deployment.
