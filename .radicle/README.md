# Radicle CI/CD

Install: `cargo install radicle-artifact radicle-job --locked`.

## CI ([`ci.sh`](ci.sh))

Runs from the repo root via **`make radicle_ci`** or **`.radicle/ci.sh`**.

1. **`git fetch rad`** and **`refs/heads/rad-ci`** when present.
2. **Orphan worktree** (`radicle-ci-<pid>`) so you can keep a local **`rad-ci`** checkout elsewhere.
3. **Restore** prior logs from **`rad/rad-ci`** into **`.radicle/ci/`** when that tree exists on the remote branch; otherwise first-run message only.
4. **`rad-job new`** / **`run`** (Explorer log URL), then **`cargo test`** (same as **`make contract_test`**) with output in **`.radicle/ci/<YYYY-MM-DD>_<full-sha>.log`**.
5. **`rad-job succeeded`** or **`failed`** for the run UUID, then **commit** all of **`.radicle/ci`** and **`git push --force rad HEAD:refs/heads/rad-ci`**.

Remote **`rad`**, branch **`rad-ci`**.

**Explorer log URL:**  
`https://radicle.network/nodes/<host>/<rid>/remotes/<nid>/tree/rad-ci/.radicle/ci/<YYYY-MM-DD>_<sha>.log`  
- **`<host>`:** **`RADICLE_LOG_NODE`** (default **`iris.radicle.xyz`**)  
- **`<nid>`:** last path segment of **`remote.rad.pushurl`** (`rad://<rid>/<nid>`)

The script **exits with the `cargo test` exit code**.

Logs on **`rad-ci`** are pruned automatically after 90 days by parsing the **`<YYYY-MM-DD>`** date prefix in each filename. Old job COBs are **not** pruned (`rad-job` has no delete subcommand).

## CD

After release assets and IPFS CIDs exist, see [`release.sh`](release.sh).

Optional: `rad-artifact attest $TAG --cid <cid>` per artifact for Radicle delegate attestation.
