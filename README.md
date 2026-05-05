# leartech-qa-management

Single source of truth for the leartech QA system. Consumed by `leartech-gate`, the future risk-assessor, the future arrivals-observer, and any other consumer that needs to know "what tests are required per service" or "what's the routing for QA notifications".

**Consumed via Renovate-pinned tags** by every consumer — pull-based, audited, easy to revert. No fan-out push from this repo.

## Phase 1+ scope (full design)

See `~/leartech/qa-architecture/qa-management-repo.md` for the complete schema and rationale. Phase 1 will add CUE schemas, cross-reference linter, auto-merge policy for additions-only PRs, repo-type-policy with risk_modifiers, service-catalog, risk-config, load-test SLAs, and notification-config.

## Session 0 spike scope (this commit)

Minimum viable for the spike. Hand-rolled YAML — no CUE schemas yet (Phase 1 hardening).

```
required-tests/
  leartech-qa-canary.yaml     — single service, single required test
gate-metadata/
  quills.yaml                 — only shift-left-tests entry
OWNERS
```

Contents:

- **`required-tests/leartech-qa-canary.yaml`** — declares which tests `leartech-gate`'s shift-left-tests quill should look for in the result-store before allowing promotion.
- **`gate-metadata/quills.yaml`** — declares which quills `leartech-gate` runs and how. Single quill in the spike: `shift-left-tests`. More quills land in Phase 1 hardening.

## How consumers find this

`leartech-gate` reads the repo at run time (Phase 1 will pin via Renovate; spike just reads `@main`):

```bash
# Inside leartech-gate Tekton task:
git clone --depth 1 https://github.com/mikelear/leartech-qa-management.git /tmp/qa-mgmt
# Or via raw GitHub API:
gh api repos/mikelear/leartech-qa-management/contents/required-tests/leartech-qa-canary.yaml \
  --jq .content -r | base64 -d
```

## Companion repos

- `mikelear/leartech-qa-canary` — test fixture service.
- `mikelear/leartech-qa-sandbox-gitops` — sandbox GitOps repo where promotion PRs land.
- `mikelear/leartech-gate` — the gate binary that reads this repo.
- `mikelear/qa-architecture` — design docs.
