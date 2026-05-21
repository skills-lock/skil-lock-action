# Marketplace publish — copy & paste

Copy/paste-ready text for publishing this Action to the GitHub Marketplace.

To publish: in this repo's UI, go to **Releases → v0.1.1 → Edit release → Marketplace section → check "Publish this Action to the GitHub Marketplace"**, then fill the fields below.

---

## Name (max 39 chars)

```
SkilLock CI
```

## Short description (max 125 chars)

```
Pin the behavior of your AI Skills. See exactly what changed in every PR.
```

## Primary category

```
Code quality
```

## Another category (optional)

```
Security
```

## Long description (Markdown supported)

```markdown
**SkilLock** pins the capability surface — shell commands, network URLs, file paths — of every [Claude Code](https://code.claude.com/docs/en/skills) and [Codex](https://developers.openai.com/codex/skills) Skill in your repository.

On every Pull Request, this Action:

1. Downloads a pinned `skil-lock` binary from the matching [release](https://github.com/skills-lock/skil-lock/releases) (verifies SHA-256).
2. Runs `skil-lock ci` against your committed `skills.lock` and `.skil-lock.yaml`.
3. Posts (or updates) a single PR comment showing every capability delta — new shell commands, new URLs, new file paths.
4. Fails the check when policy is `mode: block` and any delta is at severity ≥ medium.

Hash pinning catches tampering. SkilLock catches *what the skill is doing*.

### Quick start

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
      - uses: skills-lock/skil-lock-action@v0.1.1
        with:
          pin-binary: v0.1.1
```

### Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `pin-binary` | yes | — | `skil-lock` release tag (e.g. `v0.1.1`). No floating refs. |
| `comment` | no | `true` | Post/update PR comment with capability diff. |
| `path` | no | `.` | Repository root containing `.claude/skills/` or `.codex/skills/`. |
| `sarif` | no | `false` | Upload SARIF v2.1.0 report to GitHub Code Scanning. Requires `security-events: write`. |

### Worked example

See [skills-lock/example-claude-code-skills](https://github.com/skills-lock/example-claude-code-skills) for a runnable demo, including a `drift` branch that shows what SkilLock catches when a skill suddenly grows risky behavior.

### Related

- [skills-lock/skil-lock](https://github.com/skills-lock/skil-lock) — the CLI + lockfile spec
- [`skills.lock` v0.1 specification](https://github.com/skills-lock/skil-lock/blob/main/SPEC.md)

Apache 2.0. Not affiliated with Skil power tools, Anthropic, or OpenAI.
```

## Pricing

```
Free
```

## Icon / Color

Already declared in `action.yml`:

```yaml
branding:
  icon: 'lock'
  color: 'blue'
```

No further action needed.

---

## After publishing

- Confirm the listing appears at `https://github.com/marketplace/actions/skillock-ci` (GitHub generates the slug by lowercasing the `name:` from action.yml — `SkilLock CI` → `skillock-ci`, no hyphen between "skil" and "lock" because there isn't one in the source name).
- The first hour after publish, refresh once to confirm assets render and the install snippet is correct.
- If the README in this repo updates, the Marketplace listing does NOT auto-update — re-publish from the next release.
