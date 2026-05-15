# setup-stratt

GitHub Action that installs [stratt](https://github.com/zebpalmer/stratt) in a workflow runner.

## Usage

Pin to a commit SHA (recommended — tags are mutable, SHAs are not):

```yaml
- uses: zebpalmer/setup-stratt@<sha>  # x.y.z

- run: stratt test
```

Find the SHA for the latest release on the [releases page](https://github.com/zebpalmer/setup-stratt/releases) or via:

```sh
gh release view --repo zebpalmer/setup-stratt --json targetCommitish -q .targetCommitish
```

[Dependabot](https://docs.github.com/en/code-security/dependabot/working-with-dependabot/keeping-your-actions-up-to-date-with-dependabot) and [Renovate](https://docs.renovatebot.com/) can both update SHA pins automatically.

Pin to a specific stratt version:

```yaml
- uses: zebpalmer/setup-stratt@<sha>  # x.y.z
  with:
    version: '0.4.0'
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `version` | `latest` | stratt version to install. Omit or pass `latest` to resolve the current release. |

## Security

Every installation is verified at two layers:

1. **Checksum** — the downloaded archive is checked against the GoReleaser-published `checksums.txt` for the release.
2. **GitHub artifact attestation** — `gh attestation verify` confirms the archive was produced by the `zebpalmer/stratt` release workflow, not just hash-matched. A compromised CDN or stolen release token cannot produce an archive that passes this check.

Pinning to a commit SHA (the recommended usage above) ensures the action code itself is immutable — the two-layer binary verification plus an immutable action reference gives you a fully auditable chain from source to running binary.

## Requirements

The `gh` CLI must be available on the runner. It is pre-installed on all GitHub-hosted runners (`ubuntu-*`, `macos-*`, `windows-*`). Self-hosted runners must install it separately.

## License

Apache-2.0
