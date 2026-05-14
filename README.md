# leartech-qa-management

Single source of truth for the leartech QA system. Designed per `~/leartech/qa-architecture/qa-management-repo.md` to be consumed by every future component that needs to know "what tests are required per service", "what risk class is this service", or "where do QA notifications route".

## Current state (2026-05-14)

The repo is **strategic infrastructure that is not yet consumed**. The shift-left quill that previously read `required-tests/*.yaml` was removed from `leartech-gate` on 2026-05-14 — its release-time test-execution design reinvented K8s readiness probes + the post-deploy quill's coverage without genuine value-add. See `leartech-gate/cmd/gate-cli/main.go` for the removal commit's reasoning.

```
required-tests/
  leartech-qa-canary.yaml     — empty required_tests:[]; preserved as a
                                 placeholder for the schema. No reader today.
OWNERS                         — codeowners stub
README.md                      — this file
```

The repo is kept (not deleted) because it remains the right home for the consumers below as they land.

## Designed future consumers (not yet built)

Each of these will pull schema from this repo when implemented:

- **risk-assessor** — AST-diff → risk classification → test-pack modifier. Will read `risk-config.yaml` + `repo-type-policy.yaml`.
- **AI coverage scanner** — reads `service-catalog.yaml` to know what coverage targets exist; writes coverage gaps as issues.
- **leartech-load-testing** — reads `load-slas.yaml` per service for threshold gates.
- **Notifier framework** — reads `notification-config.yaml` for transport routing (Slack channel, GitHub repo, PagerDuty service) per event type.
- **Future copromotion / compliance quills in gate-cli** — co-required service pairs, security-attestation policy. Mirrors mqube porcupine's `basicdependency` quill.

## How future consumers find this

Pull-based via Renovate-pinned tags or raw HTTP. No fan-out push from this repo. Consumers cache locally + revalidate on signal.

```bash
# Raw HTTP (no clone):
curl -fsS https://raw.githubusercontent.com/mikelear/leartech-qa-management/main/required-tests/leartech-qa-canary.yaml

# Or shallow clone:
git clone --depth 1 https://github.com/mikelear/leartech-qa-management.git /tmp/qa-mgmt
```

## Companion repos

- `mikelear/leartech-gate` — the gate binary.
- `mikelear/leartech-qa-canary` — synthetic test-bed service.
- `mikelear/qa-architecture` — design docs (start at `qa-management-repo.md` for the full schema).
