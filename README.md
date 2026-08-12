# regula

Shared CI and dependency policy for my projects.

These are [Renovate shareable presets](https://docs.renovatebot.com/config-presets/).
They are not applied by any tool in this repo — Renovate fetches them at run time
from repositories whose `.github/renovate.json` extends them:

```json
{
    "$schema": "https://docs.renovatebot.com/renovate-schema.json",
    "enabled": true,
    "dependencyDashboard": false,
    "extends": [
        "config:recommended",
        "github>jheddings/regula:typescript"
    ]
}
```

Renovate resolves `github>jheddings/regula:typescript` to `typescript.json` in
this repository's root. The `.json` extension is never written in the reference.

## Presets

| Preset | Applies to | What it does |
| --- | --- | --- |
| `typescript` | npm projects | Groups `typescript` with the typescript-eslint family |
| `golang` | Go modules | Groups `golang.org/x/**` |
| `python` | Python projects | Labels and automerges dev tooling |

**`typescript`** exists to fix a recurring stall. `@typescript-eslint/*` declares
a `peerDependencies` range on `typescript`, so a new TypeScript major cannot
install until typescript-eslint widens it, and the upgrade PR sits red with
`ERESOLVE` until someone notices it went green. TypeScript 6 was blocked this way
for 36 days. Grouping the packages means the enabling typescript-eslint release
lands in the same PR as the major, so the upgrade resolves atomically.

**`golang`** groups `golang.org/x/**`, which release in lockstep.

**`python`** labels dev tooling as `dep:ci` and automerges it.

## Notes

These file paths are load-bearing. The reference string is committed into every
consuming repository, so renaming this repo or any preset file breaks Renovate
for each one — as a fatal config error, not a degraded run.

Repository settings, labels, and the consuming repos' `.github/renovate.json`
files are managed declaratively by [kodex](https://github.com/jheddings/kodex).
