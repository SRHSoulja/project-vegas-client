# Project Vegas Linux reference client — second setup study

**1.7.0-alpha.2 is setup-only.** Transactions are disabled. This is not the complete V1.7 release, a wallet launch, or approval to hold valuable assets. Use the empty study identity from the first session. No test ETH or sponsor refill is needed.

Supported study target: Linux x86_64, Ubuntu 24.04 (tested under WSL2). Native Windows, macOS and mobile are not supported. Installation uses system Python 3.11+, OpenSSH `ssh-keygen`, `sha256sum`, `tar` and `curl`; no compiler, Go/Rust/Node toolchain, private repository or developer cache is needed. Runtime dependencies/proving artifacts are bundled. Allow approximately 1 GB free disk for download, extraction and installation. No proof is generated in this setup session.

## Authenticate before running downloaded software

Obtain the release signing fingerprint through an independently trusted channel. For the study, the operator also provides it directly in the project conversation:

`SHA256:+YofEZpwUJ6Mhym35KDNJGozIeqDOeqCynLaMG015jc`

A key and fingerprint taken only from this GitHub page are not independent authentication. Retain the authenticated public key separately for later offline checks. A compromised hosting account without the retained signing key cannot produce a matching signed checksum.

In a Linux terminal, create a new download directory and run:

```bash
mkdir -m 700 vegas-setup-alpha2
cd vegas-setup-alpha2
base=https://github.com/SRHSoulja/project-vegas-client/releases/download/v1.7.0-alpha.2
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
curl --fail --location --proto '=https' "$base/vegas-1.7.0-alpha.2-linux-amd64.tar.gz" -o vegas-1.7.0-alpha.2-linux-amd64.tar.gz
sha256sum -c SHA256SUMS
```

**Stop if the checksum fails.** Only after both checks succeed, extract and install:

```bash
tar -xzf vegas-1.7.0-alpha.2-linux-amd64.tar.gz
study_home="$HOME/vegas-alpha2-study"
mkdir -m 700 "$study_home"
env HOME="$study_home" python3 vegas-1.7.0-alpha.2/verify-install.py vegas-1.7.0-alpha.2 --anchor "$PWD/release.pub" --install
env HOME="$study_home" "$study_home/.local/lib/project-vegas/1702/bin/vegas"
```

The installer verifies the signed manifest and every artifact offline before executing the client. It retains the signing anchor and version/hash floor. Installation is explicit, not automatic. It does not change custody/verifiers or touch existing V1.6 research files.

## The small human test

**Funding: none.** No ETH, tokens, sponsor refill, paid storage or transactions are needed. Transaction modes are disabled.

The commands above install into a separate study home. They do not replace alpha.1, change its trust record, delete your original identity or overwrite your backup. `HOME` is changed only for the child installer/client processes, not your shell. If the study directory already exists, stop and choose a new directory rather than deleting anything.

1. Choose **2 Recover identity**. Enter the **full absolute path** of your already verified encrypted backup. Because this is a separate study home, do not use `~/` to refer to your original home. Choose a **new directory that does not already exist** when prompted. Enter your passphrase privately.
2. Choose **7 Show my receive address**. Compare locally with your existing identity. Tell us only whether it matches and whether the 'shareable receive address' wording makes sense.
3. Choose **6 Inspect pending authorizations**. Does it clearly explain that missing local records do not cancel an earlier exported proof? There should be no transaction to approve in this study.
4. Quit with **0**, then start the same isolated client again:

```bash
study_home="$HOME/vegas-alpha2-study"
env HOME="$study_home" "$study_home/.local/lib/project-vegas/1702/bin/vegas"
```

Choose **7** again and confirm locally that the restored identity remains selected. Then quit.

This demonstrates signed candidate installation, clearer menu-guided recovery and retained identity selection across restart. It does not demonstrate a token transaction or full machine-loss recovery.

**Safe to report:** version, step number, success/failure, ordinary error wording, rough wait time and confusing labels. An address is public but unnecessary to share; say 'matches' or 'does not match'. **Never send:** passphrase, encrypted backup contents, spend/recovery keys, decrypted notes or witness files. No recording of secret entry is needed.

The first human session remains recorded exactly as setup and fresh-directory backup restoration. This follow-up does not replace that evidence. V1.7 stays open; full transaction integration and public-release token recovery are not claimed complete. GitHub remains the only durable source.
