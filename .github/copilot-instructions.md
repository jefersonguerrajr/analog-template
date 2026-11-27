# Copilot Instructions

## Project context

- This is an Angular 22 application built with Analog 3 and Vite 8. It is a
  full-stack app: Angular renders the UI, Analog provides file-based routes,
  and Nitro builds the server-side output.
- Use Node.js versions allowed by `package.json`
  (`^22.18.0 || ^24.3.0`).
- Keep shared framework guidance aligned with `AGENTS.md`. That file points to
  `node_modules/@analogjs/platform/AGENTS.md`; if that installed file is
  unavailable, use the Analog documentation rather than copying framework
  guidance into this repository.

## Commands

- Install dependencies: `npm install`
- Start Vite development mode: `npm start` (the Vite default is port 5173)
- Build client, SSR, prerendered pages, and server output: `npm run build`
- Rebuild on changes: `npm run watch`
- Run the Vitest suite: `npm test -- --run`
- Run one test file: `npm test -- --run src/app/app.spec.ts`
- Run one test by name:
  `npm test -- --run src/app/app.spec.ts -t "should create the app"`
- Run the built production server: `npm run preview`
- There is no lint script configured in this repository.

Vitest is configured in `vite.config.ts` to discover `**/*.spec.ts`, use
JSDOM, and load `src/test-setup.ts`. Tests use Angular TestBed plus Analog's
test setup.

## Architecture

- `src/main.ts` bootstraps the browser app with `src/app/app.ts` and
  `src/app/app.config.ts`.
- `src/main.server.ts` is the SSR entry point. It renders the same root
  component with `src/app/app.config.server.ts`, which merges server rendering
  into the browser application configuration.
- `src/app/app.ts` is the shell. Its router outlet renders Analog page routes.
- `src/app/pages/**/*.page.ts` are file-based routes. A page component must
  have a default export so Analog can discover it. For example,
  `src/app/pages/index.page.ts` is the `/` page.
- `src/server/routes/**` contains Nitro/H3 server routes. The directory and
  file name determine the URL; `src/server/routes/api/v1/hello.ts` exposes
  `/api/v1/hello`.
- `app.config.ts` supplies `provideFileRouter()`, HTTP, global error handling,
  and hydration with event replay. Preserve the shared browser/server
  providers when changing application bootstrap behavior.
- `index.html` is the Vite host document and loads `src/main.ts` plus the
  global `src/styles.css`.

## Repository conventions

- Follow the strict Angular and TypeScript settings in `tsconfig.json`:
  strict templates, strict injection parameters, strict return checks, and
  no fallthrough cases.
- Components use Angular standalone APIs. Use the current file-local pattern:
  import standalone components directly, use `template` for small inline
  templates, and `templateUrl`/`styleUrl` when the page markup or styles are
  substantial.
- Add browser UI route tests beside the code as `*.spec.ts`. Configure
  routed components with Angular's router and location testing providers, as
  demonstrated by `src/app/app.spec.ts`.
- Tailwind CSS 4 is wired through `@tailwindcss/vite` in `vite.config.ts` and
  `@import "tailwindcss"` in `src/styles.css`. Put global Tailwind imports and
  global browser rules in `src/styles.css`; keep component-specific styling in
  the component or its colocated stylesheet.
- Keep Vite plugin order and both Analog plugins in `vite.config.ts`: Analog,
  the Angular plugin, and Nitro coordinate client and SSR builds. The
  Tailwind Vite plugin belongs alongside them.
- Do not put page UI in `src/server/routes`; keep browser components under
  `src/app` and server-only handlers under `src/server/routes`.
