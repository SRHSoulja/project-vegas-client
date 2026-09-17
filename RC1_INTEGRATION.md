# V1.7 rc.1 — operator integration candidate

This is a Linux x86_64 **valueless Robinhood testnet46630** candidate, not a completed V1.7 gate or a financial release. Transactions are enabled. The next human setup study still uses [alpha.2](ALPHA2_STUDY.md); do not substitute a funded transaction study for it.

The package contains public client source, executable, native proof/recovery workers, unchanged CS/PK/VK, deployment configuration, Node runtime and license notices. It requires no private repository or development toolchain to run. Ubuntu24.04 under WSL2 is tested; this is not native Windows/macOS/mobile support. Allow2GB free disk and approximately8GB allocated RAM for the tested proving environment. Lower-memory operation has not been validated. One local proof runs at a time. Cold proving can take roughly a minute or longer; progress shows stages and elapsed time, not fabricated percentages.

## Authenticate and install separately

Retain the independently supplied signing fingerprint:

`SHA256:+YofEZpwUJ6Mhym35KDNJGozIeqDOeqCynLaMG015jc`

Do not trust a fingerprint solely because it came from the same download. Use trusted system OpenSSH, Python3.11+, curl, sha256sum and tar:

```bash
mkdir -m 700 vegas-rc1-download
cd vegas-rc1-download
base=https://github.com/SRHSoulja/project-vegas-client/releases/download/v1.7.0-rc.1
curl --fail --location --proto '=https' "$base/release.pub" -o release.pub
ssh-keygen -lf release.pub
```

Stop unless the fingerprint matches your retained anchor. Then:

```bash
curl --fail --location --proto '=https' "$base/SHA256SUMS" -o SHA256SUMS
curl --fail --location --proto '=https' "$base/SHA256SUMS.sig" -o SHA256SUMS.sig
printf 'vegas %s\n' "$(cat release.pub)" > allowed-signers
ssh-keygen -Y verify -f allowed-signers -I vegas -n vegas-reference-archive-v17 -s SHA256SUMS.sig < SHA256SUMS
```

Stop if verification fails. After success:

```bash
curl --fail --location --proto '=https' "$base/vegas-1.7.0-rc.1-linux-amd64.tar.gz" -o vegas-1.7.0-rc.1-linux-amd64.tar.gz
sha256sum -c SHA256SUMS
```

Stop on any mismatch. Only then extract and install into a new, separate home:

```bash
tar -xzf vegas-1.7.0-rc.1-linux-amd64.tar.gz
pilot_home="$HOME/vegas-rc1-pilot"
mkdir -m 700 "$pilot_home"
env HOME="$pilot_home" python3 vegas-1.7.0-rc.1/verify-install.py vegas-1.7.0-rc.1 --anchor "$PWD/release.pub" --install
env HOME="$pilot_home" "$pilot_home/.local/lib/project-vegas/1703/bin/vegas"
```

Do not delete an existing directory to make these commands succeed. Choose a new path. The original alpha installation and its keys are not changed. The authenticated installer and runtime verify every inventory entry offline. The installer never downloads code. Upgrading an existing profile is explicit and advances its version/hash floor; the old binary is retained but rejected for that upgraded profile.

## Operation map

- Identity:1 create,2 restore into a new directory,3 backup,4 verify backup,7 receive address.
- State:5 refresh notes and public claim status,6 inspect local pending authorizations. Missing journals do not revoke exported proofs. A failed receipt alone does not revoke a private proof.
- Local contacts:20 add full validated address,21 list. Use `@name` for a local alias; review the full recipient at approval. Names are not centrally authenticated.
- Gas wallet:12 create,13 show address,22 encrypted backup,23 restore. This is separate from the Vegas identity backup and uses its own password. Only valueless testnet ETH belongs here.
- Deposit:8 constructs the output/proof locally.15 approves the exact token quantity, then14 submits the deposit. The local gas account must own both the approved test token and test ETH. Deposits are user-paid.
- Private transfer:10 constructs a zero-fee self-paid transfer, then14 submits the approved proof.18 requests sponsorship with an explicitly approved quote; the pilot reserve may decline and no refill is promised.
- Sovereign exit:11 approves a whole-note exit to the exact public recipient;14 submits it with user-paid test gas.5 shows the resulting claim,16 redeems it. A ready claim is not yet paid. No sponsor, fee quote or Vegas signature is required.
-17 reconciles the exact retained signed-transaction hash. UNKNOWN is not failure. Do not replace a transaction by guessing. CLI exact-byte rebroadcast is available only for a retained signed transaction.

Review the trusted prompt before entering its digest. Proposer-supplied opaque calldata is not accepted. Private proving stays local. A cancellation/crash after export does not revoke the authorization.

**Backups:** retain encrypted Vegas identity file AND passphrase, separately from this installation. Public history cannot recreate a lost key. The optional Ethereum gas wallet has a different backup; loss of it does not remove the Vegas entitlement, but a fresh gas wallet needs independently supplied test ETH. Never send either backup, password, decrypted note or witness to support.

GitHub is the sole durable release source; the second mirror remains explicitly deferred. Canonical RPC honesty/finality and access to retained chain data remain assumptions. Sponsor unavailability is not loss of assets. A stale/missing canonical observation must stay UNKNOWN.

## Reproduce from public source

The authenticated archive includes the source subset and pinned Go/Rust/npm locks. Builders (not ordinary users) need Go1.27.1, Python3, bubblewrap and network access to pinned dependencies. From the extracted archive run `apps/reference-client/scripts/rebuild-isolated.py RELEASE GO_SDK NEW_BUILD_DIR REPORT.json`. It uses fixed namespace/module-cache paths required by gnark-crypto assembly. Compare against the signed manifest, not a filename. Frozen Rust workers have a separate `rebuild-workers.py` procedure and hashes. Reproducibility is not a security audit.
