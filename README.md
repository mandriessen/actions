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

## Design decisions

- **Repo visibility**: **Public — required, not optional**. The original 2026-04-19 audit intent was "private (matches fleet norm)" but that didn't survive contact with GitHub's personal-account mechanics: a private composite action cannot be `uses:`'d cross-repo on a personal account (only on GitHub Enterprise Cloud Organizations). Making this repo Private would break the CI of all 11 fleet consumers that depend on `setup-core`. See [`neural-leap-config/wiki/entities/actions.md`](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/entities/actions.md) for the full visibility rationale and security audit; see [`neural-leap-config/wiki/decisions/adr-045-ghec-org-migration.md`](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/decisions/adr-045-ghec-org-migration.md) for the deferred ADR on migrating to a GHEC org (which would obviate this constraint).
- **Canonical secret name**: `GH_PKG_TOKEN` (fleet plurality 47:6)
- **Scope**: install + auth + fleet antipattern guards + gitleaks. Does NOT run typecheck/lint/test — those stay per-project.
- **Future commit review**: future changes to `setup-core/action.yml` MUST be reviewed with "anyone can read this" in mind. Do not embed incident details (only IDs are safe), customer/product identifiers, or credentials (even ephemeral). Stick to orchestration steps that reference `inputs:` for sensitive values.

## Related

- [neural-leap-config/wiki/entities/actions.md](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/entities/actions.md) — canonical entity page (visibility rationale, security audit, tagging strategy, consumer list)
- [neural-leap-config/wiki/decisions/adr-045-ghec-org-migration.md](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/decisions/adr-045-ghec-org-migration.md) — deferred ADR; re-evaluation triggers that would prompt a Private migration
- [neural-leap-config/wiki/proposals/Open/composite-github-action-setup-core.md](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/proposals/Open/composite-github-action-setup-core.md) — originating proposal (2026-04-19)
- [neural-leap-config/wiki/entities/vercel-team-shared-env.md](https://github.com/mandriessen/neural-leap-config/blob/dev/wiki/entities/vercel-team-shared-env.md)
