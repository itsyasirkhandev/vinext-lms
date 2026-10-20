## What changed

<!-- One or two sentences on the change itself. -->

## Why

<!-- The problem this solves, or the issue it closes. "Closes #123" if there is one. -->

## How it was verified

<!-- Which checks you ran and what you saw. For example: `pnpm exec vp check` passes, `pnpm exec vp build` succeeds. -->

## Things a reviewer should look at

<!-- Anything that needs a second opinion, or a place where you are unsure of the approach. -->

## Checklist

- [ ] `pnpm exec vp check` passes
- [ ] `pnpm exec vp build` succeeds, if the change touches the Worker output
- [ ] No new runtime dependency was added without a reason
- [ ] Documentation was updated if the change affects the setup or deployment steps
