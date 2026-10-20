# vinext LMS

[![CI](https://github.com/itsyasirkhandev/vinext-lms/actions/workflows/ci.yml/badge.svg)](https://github.com/itsyasirkhandev/vinext-lms/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-1f6feb.svg)](LICENSE)
[![vinext](https://img.shields.io/npm/v/vinext?color=F6821F&label=vinext)](https://www.npmjs.com/package/vinext)
[![pnpm](https://img.shields.io/badge/pnpm-12.14.0-F69220?logo=pnpm&logoColor=white)](https://pnpm.io)
[![React](https://img.shields.io/badge/react-19.3.1-61DAFB?logo=react&logoColor=black)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/typescript-7.0.2-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/tailwindcss-4.3.3-06B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com)
[![Cloudflare Workers](https://img.shields.io/badge/Cloudflare_Workers-F38020?logo=cloudflare&logoColor=white)](https://developers.cloudflare.com/workers/)

A learning management system (LMS) starter built on [vinext](https://github.com/cloudflare/vinext). You write Next.js App Router code, Vite runs it during development, and the same project ships to Cloudflare Workers as an edge Worker.

Use this repository when you need a learning platform on modern edge infrastructure and you would rather not assemble the toolchain yourself. It gives you the App Router, React Server Components, Tailwind CSS, a Cloudflare Worker entrypoint, and a Workers response-store cache already wired together, so the first hour of your project goes into courses, enrollments and progress instead of configuration.

This is a starting point, not a finished LMS. The auth, database and course model are yours to build. The roadmap section below lists the LMS features this scaffold is designed to grow into.

## What you get

The repository is small on purpose. It contains the pieces that are annoying to wire up by hand, and nothing that pretends to be a course platform.

- Next.js App Router conventions (`app/` directory, layouts, route handlers, server components) with no `next dev` and no `next build`.
- Vite 8 with the vinext plugin handling the RSC build, SSR environments and the client bundle.
- Tailwind CSS 4 wired through PostCSS, with the theme already imported in `app/globals.css`.
- A Cloudflare Worker entrypoint (`vinext/server/fetch-handler`) that serves both the app and its static assets.
- A Workers response store bound as an auxiliary Worker, so cached response bodies persist between requests.
- Formatting, linting and type checking wired to `vp check`, with a pre-commit hook that runs it on staged files.

## Tech stack

| Layer                  | Choice                                  | Version         |
| ---------------------- | --------------------------------------- | --------------- |
| App framework          | vinext (Next.js App Router API on Vite) | 1.2.1           |
| Bundler and dev server | Vite+ / Vite                            | 1.1.0           |
| UI runtime             | React with React Server Components      | 19.3.1          |
| Language               | TypeScript                              | 7.0.2           |
| Styling                | Tailwind CSS                            | 4.3.3           |
| Runtime                | Cloudflare Workers                      | `nodejs_compat` |
| Cache                  | `@cloudflare/workers-response-store`    | 1.1.1           |
| Package manager        | pnpm                                    | 12.14.0         |

## Quick start

```bash
pnpm install
pnpm run dev
```

The dev server listens on `http://localhost:5173`. The port follows the `PORT` environment variable when you set one.

`pnpm install` runs `vp config` through the `prepare` script, which registers the git hooks that keep formatting and lint errors out of your commits.

## Scripts

| Command                          | What it does                                                                       |
| -------------------------------- | ---------------------------------------------------------------------------------- |
| `pnpm run dev`                   | Starts the vinext dev server on Vite with HMR.                                     |
| `pnpm run build`                 | Produces the Cloudflare Worker output in `dist/`.                                  |
| `pnpm run start`                 | Previews the built Worker locally.                                                 |
| `pnpm run deploy`                | Deploys the `lms` Worker with the Cloudflare CLI.                                  |
| `pnpm run deploy:response-store` | Deploys the `lms-response-store` auxiliary Worker used for cached response bodies. |
| `pnpm exec vp check`             | Formats, lints and type checks the project.                                        |

## Deploying to Cloudflare Workers

You need a Cloudflare account and the Cloudflare CLI authenticated.

```bash
pnpm run build
pnpm run deploy
```

The Worker name, compatibility date, compatibility flags and asset routing all live in `cloudflare.config.ts`. The response-store Worker is a separate deployment, so run `pnpm run deploy:response-store` once against a prebuilt output before the first `pnpm run deploy`.

Asset handling is set to `runWorkerFirst`, which sends every request to the Worker except files under `/_next/static`. The Worker decides what to serve and falls back to the assets binding.

## Project structure

```
.
├── app/
│   ├── api/hello/route.ts   Example route handler returning JSON
│   ├── globals.css          Tailwind CSS import and base styles
│   ├── layout.tsx           Root layout and metadata
│   └── page.tsx             Home page
├── cloudflare.config.ts     Worker definition and response-store binding
├── next.config.ts           Next.js config read by vinext
├── postcss.config.mjs       Tailwind PostCSS plugin
├── vite.config.ts           Vite plugins, dev server and cache adapters
└── AGENTS.md                Toolchain rules for coding agents
```

## Configuration

There are no environment variables to set for local development. Worker bindings are declared in `cloudflare.config.ts` rather than in `.env` files, and secrets belong in the Cloudflare dashboard or `wrangler`-style secret commands.

Two Worker names are defined there: `lms` for the application and `lms-response-store` for the cache backing store. Rename them if you deploy more than one environment of this project.

## Building the LMS features

The scaffold gives you the platform. The LMS domain still needs to be written, and the order below is a sensible one because each step depends on the previous one.

| Step | What you add            | Notes                                                                                                                       |
| ---- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 1    | Data layer and schema   | Courses, lessons, enrollments, progress. Pick a database that Cloudflare can reach, such as D1 or Hyperdrive over Postgres. |
| 2    | Authentication          | Clerk, Auth0, or your own session handling. Keep sessions in a cookie so the Worker stays stateless.                        |
| 3    | Course catalogue        | A public listing and a course detail page, rendered as server components.                                                   |
| 4    | Lesson player           | Streaming video or markdown lessons, with the completion state written back per user.                                       |
| 5    | Progress tracking       | Per-enrolment progress records, then a learner dashboard.                                                                   |
| 6    | Instructor tooling      | Course authoring, quiz and assessment grading.                                                                              |
| 7    | Email and notifications | Use a Workers binding to an email provider.                                                                                 |

Two pieces of the platform help here. Server components let a course page read the database and stream HTML without a client-side data fetch. The response store keeps rendered pages cached at the edge, which matters for catalogue pages that most visitors never log into.

## Why run an LMS at the edge

Course catalogues are read far more often than they are edited, and they are read from many places. Serving them from Workers puts the render close to the reader, and the response store means a warm catalogue page does not re-render on every request.

Streaming also matters for lesson pages. A lesson page can send the shell, the video player and the navigation as separate chunks, so the page starts painting before the slowest data query resolves.

## Related resources

- [vinext on GitHub](https://github.com/cloudflare/vinext), the framework this starter is built on
- [Cloudflare Workers documentation](https://developers.cloudflare.com/workers/), for the runtime and its APIs
- [Workers Response Store](https://developers.cloudflare.com/workers/runtime-apis/cache/), for the cache binding in use
- [Vite+ documentation](https://viteplus.dev/guide/), for the unified toolchain that wraps Vite, Vitest and the linters
- [Next.js App Router documentation](https://nextjs.org/docs/app), since vinext implements the same conventions

## Contributing

Issues and pull requests are welcome. Run `pnpm exec vp check` before you open a pull request, since the pre-commit hook runs the same checks on the files you stage. See [CONTRIBUTING.md](CONTRIBUTING.md) for the details.

## License

Released under the [MIT License](LICENSE).
