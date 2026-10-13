# Fork upstream sync

The scheduled workflow on `trunk` merges `hovancik/stretchly/trunk` into a
temporary candidate based on `fix/linux-multiscreen-break-windows`. It runs lint
and tests on Linux, macOS and Windows and builds the Linux app before
fast-forwarding the custom branch. It never resets the custom branch to upstream.

The prepare and merge jobs use the repository's write deploy key, whose private
key is stored in the `UPSTREAM_SYNC_SSH_KEY` Actions secret. GitHub's default
`GITHUB_TOKEN` cannot push upstream changes to `.github/workflows`, even with
`contents: write`. The deploy key is limited to this fork; the test jobs use the
default read-only checkout token.

To replace the key, generate a dedicated SSH key pair, add its public key in
repository Settings > Deploy keys with write access, and store its private key
in repository Settings > Secrets and variables > Actions as
`UPSTREAM_SYNC_SSH_KEY`. Remove the old deploy key after validating the replacement.
Do not use an account-wide personal access token for this workflow.

Run **Sync upstream** manually from `trunk` to retry a failed update. Enable
`validate_branch` to run the checks even when upstream has no new commits.
Concurrent sync runs are serialized so they cannot replace each other's candidate.

A failure creates one assigned attention issue with an owner mention. Further
failures refresh its body with the latest run URL without adding comments or
repeating the mention. A successful upstream update closes the issue; a later
failure creates a new alert. A validation-only run does not close an unresolved
upstream alert.
