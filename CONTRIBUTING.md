# Contributing to vinext LMS

Thanks for taking the time. This project is small, so most contributions are straightforward: fix something, clarify something, or add the LMS feature you needed.

## Getting set up

```bash
pnpm install
pnpm run dev
```

Node 24 is what the project is developed on (see `.nvmrc`). pnpm is the package manager, and the version is pinned in `package.json`.

## Before you open a pull request

Run the same checks CI runs:

```bash
pnpm exec vp check
```

That covers formatting, linting and type checking. The pre-commit hook runs `vp check --fix` on staged files, so most problems surface before you push.

If your change touches the Worker build or the deployment configuration, also run:

```bash
pnpm exec vp build
```

## Ground rules for the toolchain

This project runs the Next.js App Router API on Vite through [vinext](https://github.com/cloudflare/vinext). A few rules keep it that way:

- Do not run `next dev`, `next build` or `next lint`. Use `vp dev`, `vp build` and `vp check`.
- Do not add `next` as a dependency. The `next/*` imports resolve to vinext shims.
- Do not register `@vitejs/plugin-rsc` in `vite.config.ts`. vinext registers it when it detects an `app/` directory.
- Do not commit `.cloudflare/`, `dist/`, `.vinext/` or `.next/`. They are already in `.gitignore`.

## Commit and pull request style

Write commit messages in the imperative mood: `Add course catalogue route` rather than `Added course catalogue route`. Keep the subject under 72 characters and put the reasoning in the body when the change is not obvious.

Fill in the pull request template. If a change alters the Worker output, the cache configuration or the deployment steps, say so explicitly, because those affect anyone who has already deployed the project.

## Reporting a bug

Open a bug report with the reproduction steps, what you expected and what happened instead. Include the output of `pnpm exec vp --version` and `pnpm exec vp build` if the problem involves the build.

## Adding a dependency

Use pnpm rather than editing `package.json` by hand:

```bash
pnpm add <package>
pnpm add -D <package>
```

Keep the dependency count low. The starter is deliberately minimal so that the framework choices stay visible.

## Code of conduct

Be direct and assume good faith. Review the change, not the person who wrote it.
