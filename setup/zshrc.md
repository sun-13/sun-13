# .zshrc Setup

My zsh additions on macOS.

## op-warmup — 1Password SSH key approval warmup

Git auth (push/fetch) and commit signing both use SSH keys held in the [1Password SSH agent](https://developer.1password.com/docs/ssh/agent/). Every time 1Password restarts, each key needs a one-time approval — normally I'd have to run a throwaway `git fetch` (auth key) and make a dummy commit (signing key) just to trigger the prompts.

`op-warmup` triggers both approval prompts on demand: no network, no dummy commits.

### Prerequisites

- 1Password SSH agent enabled, with `SSH_AUTH_SOCK` pointing to its socket:

  ```sh
  export SSH_AUTH_SOCK="$HOME/Library/Group Containers/2BUA8C4S2C.com.1password/t/agent.sock"
  ```

- Git commit signing configured via 1Password (`gpg.format = ssh`, `gpg.ssh.program = /Applications/1Password.app/Contents/MacOS/op-ssh-sign`, `user.signingkey` set to the signing public key).
- The auth key item in 1Password is named so its agent comment contains `GitHub Auth Key` (check with `ssh-add -L`).

### Configure

Add to `~/.zshrc`:

```sh
op-warmup() {
  local tmp=$(mktemp)
  ssh-add -L | grep "GitHub Auth Key" > "$tmp"
  echo warmup | ssh-keygen -Y sign -n warmup -f "$tmp" > /dev/null 2>&1
  rm -f "$tmp"
  echo warmup | /Applications/1Password.app/Contents/MacOS/op-ssh-sign \
    -Y sign -n git -f <(git config user.signingkey) > /dev/null \
  && echo "ssh - GitHub signing key and auth key both approved"
}
```

### How it works

- **Auth key**: `ssh-keygen -Y sign` with a public key file asks the SSH agent (1Password) to produce a real signature with the matching private key — the same operation as an SSH authentication, so it triggers the same approval prompt. Works offline, unlike `git fetch`. (`ssh-add -L` alone is not enough: listing public keys never requests a signature, so no approval fires.)
- **Signing key**: calls `op-ssh-sign` with the same arguments git uses when signing a commit, signing a throwaway `warmup` string instead of a commit object.

Only public keys are ever read or written; private keys never leave 1Password.

### Verify

After restarting 1Password, run:

```sh
op-warmup
```

Approve the 1Password prompts that appear — it prints `ssh - GitHub signing key and auth key both approved` when done. A following `git fetch` / signed commit should go through without further prompts.

Note: 1Password scopes approvals per client application by default. Check **"approve for all applications"** on both prompts — then a single `op-warmup` run from any terminal covers git in every app (editor terminals, GUI clients) until 1Password next quits.
