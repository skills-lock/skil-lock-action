# skil-lock-action

GitHub Action wrapper for **[skil-lock](https://github.com/skills-lock/skil-lock)** — pins approved AI Skill behavior and blocks unapproved drift in CI.

> **Status: early development, private.** First public release pairs with `skil-lock` `v0.1.0`.

## What it does

On every PR, this Action:

1. Downloads a pinned `skil-lock` binary from the matching release (verifies SHA-256 against `checksums.txt`).
2. Runs `skil-lock ci` against the repo's `skills.lock` and `.skil-lock.yaml`.
3. Posts (or updates) a single PR comment showing the capability delta — shell commands, network URLs, file reads/writes — across every Skill in `.claude/skills/` and `.codex/skills/`.
4. Fails the check when policy is `mode: block` and any delta is at severity ≥ medium.

## Quick start

```yaml
name: SkilLock
on: pull_request

permissions:
  contents: read
  pull-requests: write

jobs:
  skil-lock:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: skills-lock/skil-lock-action@v0.1.0
        with:
          pin-binary: v0.1.0
          # comment: 'true'   # default
          # path: '.'         # default
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `pin-binary` | yes | — | `skil-lock` release tag to download (e.g. `v0.1.0`). No floating refs. |
| `comment` | no | `true` | Post/update a PR comment with the capability diff. |
| `path` | no | `.` | Repository root containing `.claude/skills/` or `.codex/skills/`. |

`pin-binary` must match the format `vX.Y.Z` or `vX.Y.Z-rcN`. Pinning is enforced because the binary is downloaded at runtime; floating refs would defeat the lockfile contract this Action exists to uphold.

## Permissions

The Action needs:

- `contents: read` — to check out the repo.
- `pull-requests: write` — to post/update the PR comment (omit if you set `comment: false`).

The Action uses the workflow's built-in `GITHUB_TOKEN`; no PAT required.

## Platforms

Runs on `ubuntu-*` and `macos-*` runners (amd64 + arm64). Windows runners are not currently supported by this composite (the binary is published for windows/amd64 but the composite expects a POSIX shell).

## License

[Apache 2.0](./LICENSE). Contributions covered by a one-time CLA via cla-assistant.io (see [`CONTRIBUTING.md`](./CONTRIBUTING.md)).

## Security

See [`SECURITY.md`](./SECURITY.md). Report vulnerabilities privately via [GitHub Security Advisories](https://github.com/skills-lock/skil-lock-action/security/advisories/new), not public issues.
