# Fix: `git@github.com: Permission denied (publickey)` on Windows (MSYS2/Git Bash)

## Symptom

```
$ git pull --tags origin app
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

## Cause

GitHub rejected every SSH key offered. Typical reasons:

1. `~` in MSYS2 is not `C:\Users\<you>`. It follows `$HOME` (here `/d/***`), so SSH reads keys from the wrong folder.
2. The key has a non-default name (e.g. `open`), so SSH never tries it.
3. The public key was never added to the GitHub account.

## Diagnose

```bash
echo ~; ls ~/.ssh                                   # where SSH looks, what's there
ssh -vT git@github.com 2>&1 | grep -iE "identity file|offering|Permission|Hi "
```

- No `Offering public key` line: SSH isn't picking up any key (config/name issue).
- `Offering public key` then `Permission denied`: GitHub doesn't know that key.

## Fix

**1. Generate a key**

```bash
ssh-keygen -t ed25519 -f ~/.ssh/github -C "you@email.com"
```

**2. Pin it in `~/.ssh/config`** (SSH uses the first matching `Host`; avoid duplicates)

```
Host github.com
  IdentityFile ~/.ssh/github
  IdentitiesOnly yes
```

**3. Add the public key to GitHub** (this is the step people miss)

```bash
clip < ~/.ssh/github.pub     # Windows clipboard
```

Paste at <https://github.com/settings/ssh/new> as an *Authentication Key*.
If the repo belongs to an SSO org: Settings → SSH keys → *Configure SSO* → *Authorize*.

**4. Verify**
```bash
ssh -T git@github.com        # Hi <user>! You've successfully authenticated...
git pull --tags origin app
```

## Notes

- Never share or commit the private key (`~/.ssh/github`); only `.pub` goes to GitHub.
- Generating a key only creates it locally; GitHub must be told about it.
- `source` a venv in bash with forward slashes: `source /e/path/.venv/***/activate` (backslashes are eaten by bash).

## No-SSH alternative

```bash
git remote set-url origin https://github.com/OWNER/REPO.git
```

Authenticates via Git Credential Manager or a PAT.
