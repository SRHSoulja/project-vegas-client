# Project Vegas Linux reference client — setup study

**1.7.0-alpha.1 is setup-only.** Transactions are disabled. This is not the complete V1.7 release, a wallet launch, or approval to hold valuable assets. Use a fresh empty identity. No test ETH or sponsor refill is needed.

Supported study target: Linux x86_64, Ubuntu 24.04 (tested under WSL2). Native Windows, macOS and mobile are not supported. Installation uses system Python 3.11+, OpenSSH `ssh-keygen`, `sha256sum`, `tar` and `curl`; no compiler, Go/Rust/Node toolchain, private repository or developer cache is needed. Runtime dependencies/proving artifacts are bundled. Allow approximately 1 GB free disk for download, extraction and installation. No proof is generated in this setup session.

## Authenticate before running downloaded software

Obtain the release signing fingerprint through an independently trusted channel. For the study, the operator also provides it directly in the project conversation:

`SHA256:+YofEZpwUJ6Mhym35KDNJGozIeqDOeqCynLaMG015jc`

A key and fingerprint taken only from this GitHub page are not independent authentication. Retain the authenticated public key separately for later offline checks. A compromised hosting account without the retained signing key cannot produce a matching signed checksum.

In a Linux terminal, create a new download directory and run:

```bash
mkdir -m 700 vegas-setup-alpha1
cd vegas-setup-alpha1
base=https://github.com/SRHSoulja/project-vegas-client/releases/download/v1.7.0-alpha.1
curl --fail --location --proto '=https' "$base/release.pub" -o release.pub
ssh-keygen -lf release.pub
```

**Stop unless the displayed SHA256 fingerprint matches your independently retained fingerprint above.** Then:

```bash
curl --fail --location --proto '=https' "$base/SHA256SUMS" -o SHA256SUMS
curl --fail --location --proto '=https' "$base/SHA256SUMS.sig" -o SHA256SUMS.sig
printf 'vegas %s\n' "$(cat release.pub)" > allowed-signers
ssh-keygen -Y verify -f allowed-signers -I vegas -n vegas-reference-archive-v17 -s SHA256SUMS.sig < SHA256SUMS
```

**Stop if signature verification fails.** Continue only after it succeeds:

```bash
curl --fail --location --proto '=https' "$base/vegas-1.7.0-alpha.1-linux-amd64.tar.gz" -o vegas-1.7.0-alpha.1-linux-amd64.tar.gz
sha256sum -c SHA256SUMS
```

**Stop if the checksum fails.** Only after both checks succeed, extract and install:

```bash
tar -xzf vegas-1.7.0-alpha.1-linux-amd64.tar.gz
python3 vegas-1.7.0-alpha.1/verify-install.py vegas-1.7.0-alpha.1 --anchor release.pub --install
"$HOME/.local/lib/project-vegas/1701/bin/vegas"
```

The installer verifies the signed manifest and every artifact offline before executing the client. It retains the signing anchor and version/hash floor. Installation is explicit, not automatic. It does not change custody/verifiers or touch existing V1.6 research files.

## First human tasks

Read `vegas-1.7.0-alpha.1/apps/reference-client/SETUP_STUDY.md` after authentication. Create an identity, make and verify an encrypted backup, try a wrong passphrase, inspect your receive address, refresh the empty balance, restart and inspect pending-status wording. Public RPC availability can affect balance refresh; it must not affect offline backup verification.

Choose a separate backup directory such as `~/vegas-study-backup/identity.vegas-key`; the client creates a new directory with private permissions. Keep **both the encrypted backup and passphrase**. Public history cannot reconstruct a lost key. Never send either to the study operator. Do not use an existing valuable wallet/passphrase.

For a fresh-environment restore, use a second Linux user/environment or ask the operator to guide a non-destructive move of this study's local identity directory. Do not delete your only backup. Report confusing steps/errors and perceived wait time; do not send key files, secrets, terminal recordings of secret entry, or private recipient data.

## Scope and remaining work

The alpha includes the corrected V1.6 partial-conflict journal behavior in native source, but transaction modes are intentionally disabled for this first human session. Full transaction integration, token-backed public-release recovery, update drills and additional human studies remain V1.7 work. Automated backup tests are not human evidence.

GitHub is the sole durable distribution source. Second-mirror/Arweave publication remains deferred. The chain is authoritative; mirrors and sponsors cannot authorize private spending. No sponsor refill, permanent-storage purchase or chain transaction accompanies this release.

## Public source and reproduction

The authenticated archive contains an allowlisted client source subset, pinned dependency locks and license notices; it does not publish the private project repository. Source commit and all binary/artifact hashes are in `manifest.json`.

Release builders: use Go 1.27.1 and Linux `bwrap`, then invoke `apps/reference-client/scripts/canonical-build.py SOURCE_ROOT GO_SDK_ROOT NEW_WORK_DIRECTORY`. Its canonical namespace paths are part of the recipe because gnark-crypto assembly retains absolute module-cache paths despite `-trimpath`. It creates a fresh dependency/build cache and outputs `NEW_WORK_DIRECTORY/vegas`. Compare its SHA256 to manifest role `client`. The Go-client byte-equality result does not itself establish reproduction of the separately bundled frozen Rust workers or Node distribution.

## Second setup candidate

[Alpha.2 guided restore/restart study](ALPHA2_STUDY.md) is available as a separate authenticated candidate. Its guide uses an isolated study home to preserve the existing alpha.1 installation and identity. No transaction funding is required. Alpha.1 release assets and the original instructions above remain unchanged. Full V1.7 transaction/recovery acceptance remains open.

The separately versioned [rc.1 transaction integration candidate](RC1_INTEGRATION.md) is for operator validation. The next human setup task remains alpha.2; V1.7 is open.

[rc.2](RC2_INTEGRATION.md) is the separately authenticated path-guidance fix, with a prepared unfunded empty-gas-wallet study. Existing alpha/rc releases are preserved.
