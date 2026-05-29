# setup-stratt

GitHub Action that installs [stratt](https://github.com/stratt-sh/stratt) in a workflow runner. Verifies the downloaded archive's checksum *and* its GitHub artifact attestation before executing anything.

## Usage

### Recommended: pin the action major

```yaml
permissions:
  contents: read
  attestations: read   # required for `gh attestation verify`

steps:
  - uses: stratt-sh/setup-stratt@v0
  - run: stratt all
```

`@v0` is a floating tag that fast-forwards on each non-breaking action release. You get bug fixes for free; you won't get a breaking action change without explicitly bumping to `@v1`.

By default, the action installs the latest stratt CLI release within the action's own major — so `@v0` installs the latest `stratt v0.x.y`. When stratt cuts `1.0.0`, a parallel `setup-stratt@v1` ships with `version: v1` as its default.

### Pin the action by SHA (highest security)

Tags are mutable; commit SHAs aren't. For high-security pipelines, pin the action to a SHA and let Dependabot or Renovate update it:

```yaml
- uses: stratt-sh/setup-stratt@<sha>  # v0.3.0
```

Find the SHA via `gh release view --repo stratt-sh/setup-stratt --json targetCommitish -q .targetCommitish`.

### Override the stratt CLI version

```yaml
- uses: stratt-sh/setup-stratt@v0
  with:
    version: v0.5.1        # exact pin (fully reproducible builds)
    # version: v0.5        # latest v0.5.x
    # version: v0          # latest v0.x.y (default behavior)
    # version: latest      # latest stable across any major
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `version` | `v0` | stratt version selector. Accepts `vN` (major), `vN.M` (minor), `vN.M.P` (exact), or `latest`. |
| `require-attestation` | `true` | When `true`, fails the job if `gh attestation verify` doesn't pass. Set to `false` only if your org forbids granting `attestations: read`. |

## Required permissions

The attestation check fetches the Sigstore bundle from GitHub's attestations API. The calling job must grant:

```yaml
permissions:
  attestations: read
```

`id-token: write` is **not** needed — verification is read-only on GitHub's side; the Sigstore signature check happens locally against the public-good trust root.

If your org policy forbids granting `attestations: read`, set `require-attestation: false`. Checksum verification still runs unconditionally.

## Security model

Each install runs two independent checks before stratt is ever executed:

1. **Checksum** — the downloaded archive is verified against the GoReleaser-published `checksums.txt` for the release.
2. **GitHub artifact attestation** — `gh attestation verify` confirms the archive was built by `stratt-sh/stratt`'s release workflow. A compromised CDN, a stolen release token, or a typosquatted release in another repo cannot produce an archive that passes this check.

The verification is run by `gh` (a trusted, independent binary on the runner) *before* the stratt binary is exec'd, so a tampered binary cannot fake its own verification.

For an even tighter chain, pin the action to a SHA (above) so the installer code itself is immutable.

## Requirements

The `gh` CLI must be on the runner. It's pre-installed on all GitHub-hosted runners (`ubuntu-*`, `macos-*`, `windows-*`). Self-hosted runners must install it separately.

## License

Apache-2.0
