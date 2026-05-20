# Contributing to skil-lock-action

Thanks for your interest. This repository wraps the [skil-lock CLI](https://github.com/skills-lock/skil-lock) as a GitHub Action. The surface is intentionally narrow: a composite action that downloads a pinned binary, runs `skil-lock ci`, and posts a PR comment.

## Quick start

This repo has no Go or build tooling — the composite is pure shell + `gh`. To test changes locally:

```bash
# Lint the YAML
yamllint action.yml .github/workflows/*.yml || true

# Verify the composite logic manually against the binary
gh release download v0.1.0-rc1 --repo skills-lock/skil-lock \
  --pattern '*linux_amd64.tar.gz' --pattern 'checksums.txt'
sha256sum -c <(grep '_linux_amd64.tar.gz' checksums.txt)
tar -xzf skil-lock_*_linux_amd64.tar.gz
./skil-lock version
```

End-to-end testing happens in CI via `.github/workflows/ci.yml`, which exercises the composite against the fixture in `testdata/`.

## Pull requests

1. **Open an issue first** for non-trivial changes — keeps scope discussions out of PR review.
2. **One logical change per PR.** Squash-merge is the default.
3. **Tests required.** Changes to `action.yml` must be exercised by `.github/workflows/ci.yml`.
4. **Sign your commits.** SSH or GPG. `git config --global commit.gpgsign true`.
5. **CLA.** The first PR from a new contributor triggers a one-time CLA via [cla-assistant.io](https://cla-assistant.io).
6. **Pin everything.** Third-party actions in this repo and the CLI binary must be pinned to exact SHAs or release tags. No floating refs.

## Reporting bugs / security issues

- **Bugs:** open an issue with a reproduction.
- **Security vulnerabilities:** see [`SECURITY.md`](./SECURITY.md). Do **not** file a public issue.

## License

By contributing, you agree your contributions will be licensed under the [Apache License 2.0](./LICENSE).
