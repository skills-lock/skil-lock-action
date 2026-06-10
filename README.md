# skil-lock-action

GitHub Action wrapper for **[skil-lock](https://github.com/skills-lock/skil-lock)** - pins approved AI Skill behavior and blocks unapproved drift in CI.

> **Status: public.** Action `v0.2.1` pairs with `skil-lock` CLI `v0.2.1`. Action v0.2.1 adds cosign keyless verification of the release checksums before they are trusted (`verify-signature` input). CLI v0.2.1 adds exhaustive sibling-file digests and PR-scoped approvals; v0.2.0 added per-script digests, signed releases (cosign + SLSA provenance + SBOM), and a documented detection boundary.

## What it does

On every PR, this Action:

1. Downloads a pinned `skil-lock` binary from the matching release (verifies SHA-256 against `checksums.txt`).
2. Runs `skil-lock ci` against the repo's `skills.lock` and `.skil-lock.yaml`.
3. Posts (or updates) a single PR comment showing the capability delta - shell commands, network URLs, file reads/writes - across every Skill in `.claude/skills/` and `.codex/skills/`.
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
      - uses: skills-lock/skil-lock-action@v0.2.1
        with:
          pin-binary: v0.2.1
          # comment: 'true'   # default
          # path: '.'         # default
          # sarif: 'false'    # default; set 'true' to upload to Code Scanning
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `pin-binary` | yes | - | `skil-lock` release tag to download (e.g. `v0.2.1`). No floating refs. |
| `comment` | no | `true` | Post/update a PR comment with the capability diff. |
| `path` | no | `.` | Repository root containing `.claude/skills/` or `.codex/skills/`. |
| `sarif` | no | `false` | Produce a SARIF v2.1.0 report and upload it to GitHub Code Scanning. Requires `security-events: write` permission in the calling workflow. |
| `verify-signature` | no | `auto` | Cosign keyless verification of the release's `checksums.txt` against the skil-lock release workflow's GitHub OIDC identity, before the checksum file is trusted. `auto` verifies when cosign is available and the pinned release is signed (v0.2.0+), warning and falling back to checksum-only otherwise; `true` makes verification mandatory; `false` skips it. |

`pin-binary` must match the format `vX.Y.Z` or `vX.Y.Z-rcN`. Pinning is enforced because the binary is downloaded at runtime; floating refs would defeat the lockfile contract this Action exists to uphold.

The sha256 comparison alone proves the archive matches `checksums.txt`; `verify-signature` additionally proves `checksums.txt` itself was produced by the `skills-lock/skil-lock` release workflow (cosign keyless, GitHub OIDC). When verification is enabled the Action installs cosign via the SHA-pinned `sigstore/cosign-installer` step (GitHub-hosted runners no longer ship it).

## Permissions

The Action needs:

- `contents: read` - to check out the repo.
- `pull-requests: write` - to post/update the PR comment (omit if you set `comment: false`).
- `security-events: write` - only when `sarif: true`, to upload to Code Scanning.

The Action uses the workflow's built-in `GITHUB_TOKEN`; no PAT required.

## GitHub Code Scanning (SARIF)

Set `sarif: true` to also upload findings to GitHub Code Scanning so they appear inline in the PR diff and in the repo's Security tab. The PR comment is unaffected - both surfaces show the same data.

```yaml
permissions:
  contents: read
  pull-requests: write
  security-events: write
steps:
  - uses: actions/checkout@v6
  - uses: skills-lock/skil-lock-action@v0.2.1
    with:
      pin-binary: v0.2.1
      sarif: true
```

Severity mapping: `high → error`, `medium → warning`, `low|info → note`.

## Platforms

Runs on `ubuntu-*` and `macos-*` runners (amd64 + arm64). Windows runners are not currently supported by this composite (the binary is published for windows/amd64 but the composite expects a POSIX shell).

## License

[Apache 2.0](./LICENSE). Contributions covered by a one-time CLA via cla-assistant.io (see [`CONTRIBUTING.md`](./CONTRIBUTING.md)).

## Security

See [`SECURITY.md`](./SECURITY.md). Report vulnerabilities privately via [GitHub Security Advisories](https://github.com/skills-lock/skil-lock-action/security/advisories/new), not public issues.
