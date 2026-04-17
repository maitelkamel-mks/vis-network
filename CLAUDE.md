# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Build
npm run build              # Full build (declarations + code + legacy formats + assets)
npm run build:code         # Build only (Rollup, modern formats)
npm run build:watch        # Watch mode

# Test
npm run test:unit          # Mocha unit tests
npm run test:e2e:functional  # Cypress functional E2E tests (headless)
npm run test:e2e:gui       # Open Cypress GUI
npm run test:e2e:visual    # Visual regression tests

# Run a single unit test file
npx mocha --exit test/<filename>.test.ts

# Run a specific Cypress spec
npx cypress run --spec cypress/e2e/functional/<spec>.spec.ts

# Lint / format
npm run lint               # ESLint
npm run lint-fix           # ESLint auto-fix
npm run style              # Prettier check
npm run style-fix          # Prettier auto-fix
```

## Architecture

**vis-network** is a browser-based network graph visualization library rendering on HTML Canvas. It is distributed in multiple formats (UMD, ESM) with peer/standalone/esnext variants.

### Entry points

- `lib/entry-esnext.ts` — primary export (re-exports `Network`, parsers, options)
- `lib/entry-peer.ts` — peer-dependencies build
- `lib/entry-standalone.ts` — bundled-dependencies build

### Core modules (`lib/network/`)

- `Network.js` — top-level class; wires together all modules
- `modules/NodesHandler.js` / `EdgesHandler.js` — data management for nodes and edges
- `modules/CanvasRenderer.js` / `Canvas.js` — render pipeline; all drawing goes through Canvas API
- `modules/PhysicsEngine.js` — simulation loop; delegates to physics models in `modules/components/physics/`
- `modules/LayoutEngine.js` — hierarchical and force-directed layouts
- `modules/Clustering.js` — grouping/collapsing of nodes
- `modules/InteractionHandler.js` — zoom, pan, pointer events
- `modules/ManipulationSystem.js` — UI for adding/editing/deleting nodes and edges
- `modules/SelectionHandler.js` — selection state
- `modules/View.js` — viewport / camera transform
- `modules/components/nodes/` — one file per node shape (box, circle, ellipse, image, etc.)
- `modules/components/edges/` — edge rendering (bezier, straight, dynamic curves)
- `options.ts` — full default options tree and validation

### Parsers

- `network/dotparser.js` — DOT language input
- `network/gephiParser.ts` — Gephi JSON input

### Build system

Two Rollup configs coexist:
- `rollup.build.js` — modern outputs (peer / standalone / esnext, ESM + CJS + minified)
- `rollup.config.js` — legacy UMD output into `/dist`

TypeScript declarations are generated separately via `tsconfig.declarations.json` into `/declarations`, then copied to `/dist/types`.

### Tests

- **Unit tests** — Mocha + Chai + Sinon, files in `test/`, pattern `**/*.test.{ts,js}`
- **E2E functional** — Cypress specs in `cypress/e2e/functional/`
- **E2E visual** — Cypress snapshot tests in `cypress/e2e/visual/`; regenerate baselines with `npm run test:e2e:visual:base:latest`
