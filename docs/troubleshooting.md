# SSH Troubleshooting Guide

Common SSH issues and their solutions when connecting to GitHub.

## Table of Contents

- [Quick Diagnostic Commands](#quick-diagnostic-commands)
- [Permission Denied (publickey)](#permission-denied-publickey)
- [Could Not Open Connection to Agent](#could-not-open-connection-to-agent)
- [Wrong Key Being Used](#wrong-key-being-used)
- [Bad Configuration Option: UseKeychain](#bad-configuration-option-usekeychain)
- [Host Key Verification Failed](#host-key-verification-failed)
- [Connection Timeout](#connection-timeout)
- [Key Permission Issues](#key-permission-issues)

## Quick Diagnostic Commands

Run these commands to diagnose issues:

```bash
# Test GitHub connection with verbose output
ssh -vT git@github.com

# List keys loaded in ssh-agent
ssh-add -l

# Check SSH config syntax
ssh -G git@github.com

# Display your public key
cat ~/.ssh/id_ed25519.pub
```

---

## Permission Denied (publickey)

### Error Message

```
Permission denied (publickey).
fatal: Could not read from remote repository.
```

### Possible Causes and Solutions

#### 1. Key Not Added to GitHub

**Check:** View your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

**Fix:** Add the key to [GitHub SSH Settings](https://github.com/settings/keys).

#### 2. Key Not Loaded in ssh-agent

**Check:** List loaded keys:

```bash
ssh-add -l
```

If you see `The agent has no identities`, your key isn't loaded.

**Fix:** Add your key:

```bash
ssh-add ~/.ssh/id_ed25519
```

#### 3. Wrong Key Being Used

**Check:** Use verbose mode to see which key is offered:

```bash
ssh -vT git@github.com
```

Look for:

```
debug1: Offering public key: /home/user/.ssh/id_ed25519
```

**Fix:** Ensure `~/.ssh/config` specifies the correct `IdentityFile`.

#### 4. SSH Agent Not Running

**Fix:** Start the agent:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

---

## Could Not Open Connection to Agent

### Error Message

```
Could not open a connection to your authentication agent.
```

### Solution

Start the SSH agent:

**Linux/macOS:**

```bash
eval "$(ssh-agent -s)"
```

**Windows PowerShell (Admin):**

```powershell
Start-Service ssh-agent
```

**Git Bash:**

```bash
eval "$(ssh-agent -s)"
```

---

## Wrong Key Being Used

### Symptoms

- Authentication fails even though key is added to GitHub
- Verbose output shows a different key being offered

### Solution

#### Option 1: Specify Key in SSH Config

```ssh-config
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```

The `IdentitiesOnly yes` option prevents SSH from trying other keys.

#### Option 2: Specify Key in Command

```bash
ssh -i ~/.ssh/id_ed25519 -T git@github.com
```

---

## Bad Configuration Option: UseKeychain

### Error Message

```
/Users/you/.ssh/config: line 3: Bad configuration option: usekeychain
```

### Cause

`UseKeychain` is macOS-specific. This error occurs on:
- Linux systems
- Older macOS versions
- Non-Apple builds of SSH

### Solution

Add `IgnoreUnknown` before `UseKeychain`:

```ssh-config
Host github.com
    IgnoreUnknown UseKeychain
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519
```

Or remove `UseKeychain` on non-macOS systems.

---

## Host Key Verification Failed

### Error Message

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

### Cause

GitHub's host key changed (rare) or your `known_hosts` file is corrupted.

### Solution

Remove old GitHub entry and reconnect:

```bash
ssh-keygen -R github.com
ssh -T git@github.com
```

When prompted, verify the fingerprint matches [GitHub's published fingerprints](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints):

```
SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU (ED25519)
SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8 (RSA)
SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM (ECDSA)
```

---

## Connection Timeout

### Error Message

```
ssh: connect to host github.com port 22: Connection timed out
```

### Possible Causes

| Cause | Solution |
|-------|----------|
| Firewall blocking port 22 | Use SSH over HTTPS (port 443) |
| Network issues | Check internet connection |
| Corporate proxy | Configure proxy settings |

### Solution: Use SSH over HTTPS

Add to `~/.ssh/config`:

```ssh-config
Host github.com
    HostName ssh.github.com
    Port 443
    User git
    IdentityFile ~/.ssh/id_ed25519
```

Test:

```bash
ssh -T -p 443 git@ssh.github.com
```

---

## Key Permission Issues

### Error Message

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for '/home/user/.ssh/id_ed25519' are too open.
```

### Solution

Fix permissions:

**Linux/macOS:**

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/config
```

**Windows (PowerShell):**

```powershell
icacls $env:USERPROFILE\.ssh\id_ed25519 /inheritance:r /grant:r "$env:USERNAME:R"
```

---

## Verbose Debugging

For detailed troubleshooting, use multiple `-v` flags:

```bash
# Level 1 - Basic debug
ssh -vT git@github.com

# Level 2 - More detail
ssh -vvT git@github.com

# Level 3 - Maximum verbosity
ssh -vvvT git@github.com
```

### What to Look For

| Output | Meaning |
|--------|---------|
| `Offering public key:` | Shows which key is being tried |
| `Server accepts key:` | Key was accepted |
| `No more authentication methods` | All keys rejected |
| `Connection established` | SSH connection successful |

---

**Still Having Issues?**

- [GitHub SSH documentation](https://docs.github.com/en/authentication/troubleshooting-ssh)
- [Contact GitHub Support](https://support.github.com/)
