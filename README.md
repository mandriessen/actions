# @mandriessen/actions

Reusable composite GitHub Actions for the Neural Leap fleet.

## Actions

### `setup-core@v1`

Handles auth + install for any consumer of `@mandriessen/core`, plus fleet-wide antipattern guards (Mandate 5/8 enforcement, plaintext-secret detection).

**Canonical replacement** for the ~80 lines of per-repo boilerplate in every consumer's `pr-validation.yml`.

```yaml
- uses: mandriessen/actions/setup-core@v1
  with:
    node-version: '24'
    gh-pkg-token: ${{ secrets.GH_PKG_TOKEN }}  # fleet-canonical secret name (per 2026-04-19 audit)
```

See `setup-core/action.yml` for inputs, outputs, and implementation.

## Rollout

- `v1` — 2026-04-19 (audit items #4 + #5)
- Fleet migration: canary on TestHarnessE2E, then 11 remaining consumers.

## Design decisions (2026-04-19 audit defaults)

- **Repo visibility**: private (matches fleet norm)
- **Canonical secret name**: `GH_PKG_TOKEN` (fleet plurality 47:6)
- **Scope**: install + auth + fleet antipattern guards + gitleaks. Does NOT run typecheck/lint/test — those stay per-project.

## Related

- [neural-leap-config/wiki/proposals/Open/composite-github-action-setup-core.md](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/proposals/Open/composite-github-action-setup-core.md)
- [neural-leap-config/wiki/entities/vercel-team-shared-env.md](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/entities/vercel-team-shared-env.md)
