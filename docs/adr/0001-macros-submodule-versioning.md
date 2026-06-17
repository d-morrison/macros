# 1. Versioning strategy for `macros` as a git submodule

Date: 2026-06-17

## Status

Accepted

## Context

`macros` is consumed as a **git submodule** by several downstream repositories:

- `ucdavis/epi204` — submodule at `macros/`
- `d-morrison/rme` — submodule at `latex-macros/` (currently `branch = main`)
- `d-morrison/qwt` — submodule at `macros/`
- `d-morrison/rpt` — submodule at `vignettes/macros/`

Separately, `d-morrison/gha` recently introduced a **sliding major-version tag**:
a `slide-major-tag.yml` workflow re-points the `v1` tag at the tip of `main` on
every merge, and consumers pin to `uses: d-morrison/gha/...@v1`.
That pattern lets Actions consumers ride a stable major line without editing a
SHA on every fix.

We considered replicating the sliding-tag system in `macros` so that its
submodule consumers could likewise track a stable `v1` instead of `main` or an
arbitrary pinned commit.
Three shapes were on the table:

1. **Sliding `v1` tag** — mirror gha's workflow byte-for-byte.
2. **Sliding `v1` branch** — keep a `v1` branch at the latest release tip.
3. **Both** — a `v1` tag for version pinning plus a `v1` branch for tracking.

### Key technical finding (verified empirically)

The two consumption models are not symmetric:

- **GitHub Actions** resolves `uses: …@v1` against a tag directly, so a sliding
  *tag* is the natural fit for gha.
- **Git submodules** update via `git submodule update --remote`, which resolves
  the configured `submodule.<name>.branch` against `refs/remotes/origin/<branch>`.
  Tags never populate that remote-tracking namespace, so pointing `branch` at a
  tag fails:

  ```
  fatal: Unable to find refs/remotes/origin/v1 revision in submodule path 'sub'
  ```

  A moving *branch* works with `--remote`; a moving *tag* does not.

So gha's sliding-*tag* design does **not** translate one-to-one to submodule
consumption.
Only a sliding *branch* could be followed by the standard submodule command.

### The deeper problem: a sliding ref does not remove the manual bump

The motivation for a sliding ref was to avoid hand-editing a pinned commit on
every macros change.
But a submodule reference is a **gitlink** — a specific commit recorded in the
superproject's tree and index.
Whatever ref the submodule nominally tracks, the recorded commit only advances
when someone runs `git submodule update --remote` (or checks out a new commit)
**and commits the result in the consumer repo**.

That commit-and-push step is manual either way.
A sliding `v1` branch would change *which* commit `--remote` resolves to, but it
would not make the consumers update or commit themselves.
The automation benefit that justified the sliding tag in gha (Actions re-resolves
`@v1` at run time, with nothing to commit) simply does not exist for submodules.

## Decision

**Keep consuming `macros` from `main`. Do not add a sliding tag or branch.**

Concretely:

- No `slide-major-tag.yml` workflow is added to `macros`.
- No `v1.0.0` / `v1` tag or `v1` branch is cut at this time.
- Consumers continue to record explicit submodule commits; `rme` keeps
  `branch = main`, and the others continue to bump on demand.

## Consequences

- `macros` keeps shipping from a single `main` line, which matches its current
  reality: one active development line, no released majors, and no need to serve
  incompatible versions to different consumers simultaneously.
- Updating a consumer stays a deliberate, reviewable act:
  `git submodule update --remote <path>` (for the `branch = main` case) or an
  explicit `git -C <path> checkout <sha>`, followed by a commit in the consumer.
- We avoid standing up machinery (a macros CI workflow plus per-consumer
  `.gitmodules` edits) that would not actually remove the manual step.
- The gha sliding-*tag* remains correct **for gha**: that benefit is specific to
  GitHub Actions resolving `@v1` at run time, not a general versioning win we are
  failing to copy.

### When to revisit

Reopen this decision if any of the following become true:

- `macros` needs to serve **incompatible major versions** to different consumers
  at the same time (a real reason to pin to a major line).
- We adopt automation that genuinely closes the loop — e.g. a Dependabot
  `gitsubmodule` config or a bot that runs `git submodule update --remote` and
  opens PRs in each consumer.
  In that case the target ref must be a **branch** (e.g. a sliding `v1`), because
  `git submodule update --remote` cannot follow a tag (see the finding above).
