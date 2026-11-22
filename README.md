# STREAM Connect Agent releases

This repository publishes signed releases of the STREAM Connect Agent, the
on-premises agent that connects hospital systems to MOYAFLOW STREAM. It holds
release files only; the source code is not public.

Download releases through `https://install.moyaflow.de/stream-connect-agent/`,
not through GitHub URLs. The installation guide is at
<https://docs.stream.moyaflow.de/connect-agent/>.

## Files in each release

Release `v<version>` contains:

| File | Content |
| --- | --- |
| `stream-connect-agent_<version>_linux_amd64.tar.gz` | The agent, `install.sh`, systemd unit, and licenses |
| `SHA256SUMS` | SHA-256 checksums of all files in the release |
| `stream-connect-agent_<version>.cdx.json` | Software bill of materials (CycloneDX) |
| `stream-connect-agent_<version>.intoto.jsonl` | SLSA build provenance |
| `manifest.json` | Version, source revision, and checksum of every file |
| `current.json` | The latest version; served at `install.moyaflow.de/stream-connect-agent/current.json` |
| `*.sigstore.json` | Sigstore signature bundles for the tarball, `SHA256SUMS`, `manifest.json`, and `current.json` |

Published releases cannot be changed or deleted. A faulty release is fixed by a
new version; older versions stay available for rollback.

## Verify a download

You need [cosign](https://docs.sigstore.dev/cosign/system_config/installation/)
2.4 or later, and `slsa-verifier` for the provenance check.

```bash
VERSION=1.2.0
BASE=https://install.moyaflow.de/stream-connect-agent/$VERSION
TARBALL=stream-connect-agent_${VERSION}_linux_amd64.tar.gz

curl -fLO "$BASE/$TARBALL" -fLO "$BASE/$TARBALL.sigstore.json"

# 1. The tarball was signed by the MOYAFLOW release workflow.
cosign verify-blob "$TARBALL" \
  --bundle "$TARBALL.sigstore.json" \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  --certificate-identity https://github.com/moyaflow/stream/.github/workflows/release-connect-agent.yml@refs/heads/main

# 2. Optional: the tarball was built from MOYAFLOW's source by GitHub Actions.
curl -fLO "$BASE/stream-connect-agent_${VERSION}.intoto.jsonl"
slsa-verifier verify-artifact "$TARBALL" \
  --provenance-path "stream-connect-agent_${VERSION}.intoto.jsonl" \
  --source-uri github.com/moyaflow/stream \
  --source-branch main
```

Both commands print `Verified OK` / `PASSED` on success. Do not install a
tarball that fails verification.

## Security

See [SECURITY.md](SECURITY.md) to report a vulnerability.

## License

Proprietary; see [LICENSE](LICENSE). Third-party licenses are in the
`licenses/` directory of each tarball.
