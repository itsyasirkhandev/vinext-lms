<!--VITE PLUS START-->

# Using Vite+, the Unified Toolchain for the Web

This project is using Vite+, a unified toolchain built on top of Vite, Rolldown, Vitest, tsdown, Oxlint, Oxfmt, and Vite Task. Vite+ wraps runtime management, package management, and frontend tooling in a single global CLI called `vp`. Vite+ is distinct from Vite, and it invokes Vite through `vp dev` and `vp build`. Run `vp help` to print a list of commands and `vp <command> --help` for information about a specific command.

Docs are local at `node_modules/vite-plus/docs` or online at https://viteplus.dev/guide/.

## Built-in Commands vs Scripts

`vp <name>` runs a built-in command. `vp run <name>` runs a `package.json` script or a `vite.config.ts` task. Scripts cannot overwrite built-ins, so `vp dev` and `vp run dev` may do different things. Check `package.json` and `vite.config.ts` first, and run `vp run <name>` when the project defines a script or task with that name.

## Tool Versions

Run `vp toolchain` to show versions and relationships in the active Vite+
release. Add a tool name to select part of the graph. For example, run
`vp toolchain vite`. Use `--global` to ignore the local `vite-plus` package. Use
`vp why <package>` to show the package-manager dependency graph.

## Review Checklist

- [ ] Run `vp install` after pulling remote changes and before getting started.
- [ ] Run `vp check` and `vp test` to format, lint, type check and test changes.
- [ ] Check if there are `vite.config.ts` tasks or `package.json` scripts necessary for validation, run via `vp run <script>`.
- [ ] If setup, runtime, or package-manager behavior looks wrong, run `vp env doctor` and include its output when asking for help.

<!--VITE PLUS END-->

<!-- BEGIN:vinext-agent-rules -->

# vinext — Next.js API on Vite (already migrated, no Next.js toolchain)

This is a vinext project (`vinext@1.2.1`, targets Next 16.x). Do not migrate it.
Vite 8 runs dev/build — never run `next dev/build/start/lint`. Use `vp dev`,
`vp build`, `vp preview` per `package.json:6-13`.

- `app/`, `pages/`, `public/`, `next.config.*` work as-is. vinext reads
  `next.config.*` for redirects/rewrites/headers/basePath/i18n/images only —
  webpack/Turbopack config is ignored, use Vite plugins.
- `next/*` imports (`link`, `image`, `navigation`, `headers`, `cache`, etc.)
  resolve to vinext shims. Do not rewrite them to `vinext/*`.
- Do not register `@vitejs/plugin-rsc` yourself — vinext auto-registers it when
  `app/` is detected. React comes from this project's `react` deps, not a
  bundled copy.
- `runtime` / `preferredRegion` do not choose placement. `next/image` (no
  build-time optimization) and `next/font/*` (CDN/runtime injection) are partial.

Docs are online-only here: `next` is not installed so there is no
`node_modules/next/dist/docs`, and vinext ships only `dist/`. Use
https://nextjs.org/docs/app for features, then deltas at
https://vinext.dev/docs/reference/differences and https://vinext.dev/compatibility.
<!-- END:vinext-agent-rules -->
