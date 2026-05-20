# Security Policy

## Reporting a Vulnerability

**Please do not file a public GitHub Issue.** Vulnerabilities should be reported privately so we can fix and disclose responsibly.

### Preferred: GitHub Security Advisories

Use the **"Report a vulnerability"** button at:
<https://github.com/skills-lock/skil-lock-action/security/advisories/new>

This opens a private channel with the maintainers, gives you a CVE-eligible identifier, and tracks the fix to disclosure.

### Backup: Email

If you cannot use GHSA for any reason, email **security@skil-lock.dev**. Forwarded to the maintainer; expect acknowledgement within 72 hours.

## Scope

In scope:

- This composite Action (`action.yml` and its shell steps)
- Binary download + checksum verification of `skil-lock` releases

Out of scope:

- Vulnerabilities in the `skil-lock` CLI itself — report at <https://github.com/skills-lock/skil-lock/security/advisories/new>
- Vulnerabilities in third-party actions or runner images (report upstream)
- Vulnerabilities in the SKILL.md format (report to the runtime vendor — Anthropic for Claude Code, OpenAI for Codex)
- Social engineering, physical access, or denial-of-service against shared infrastructure

## Disclosure

- We aim to acknowledge reports within **72 hours**.
- Fix and coordinated public disclosure target: **90 days** from acknowledgement, or sooner if a fix lands faster.
- Reporters who wish to be credited will be named in the GHSA and release notes.

## Supply Chain

This Action downloads pinned `skil-lock` release binaries from <https://github.com/skills-lock/skil-lock/releases> and verifies SHA-256 checksums against the release's `checksums.txt` before executing.

The `pin-binary` input is required and rejects floating refs — every workflow run downloads exactly one tagged release.
