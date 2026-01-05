# SSH Key Setup for macOS

Complete guide for setting up SSH key authentication on macOS with Keychain integration.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Open Terminal](#step-1-open-terminal)
- [Step 2: Generate SSH Key](#step-2-generate-ssh-key)
- [Step 3: Configure SSH Config](#step-3-configure-ssh-config)
- [Step 4: Add Key to SSH Agent](#step-4-add-key-to-ssh-agent)
- [Step 5: Copy Public Key](#step-5-copy-public-key)
- [Step 6: Add Key to GitHub](#step-6-add-key-to-github)
- [Step 7: Test Connection](#step-7-test-connection)

## Prerequisites

- macOS 10.12.2 (Sierra) or later
- Terminal (built into macOS)

## Step 1: Open Terminal

Open Terminal using one of these methods:

- Press `Cmd + Space`, type "Terminal", press Enter
- Go to **Applications** → **Utilities** → **Terminal**

## Step 2: Generate SSH Key

Run the following command, replacing the email with your GitHub email:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

When prompted:

```
Enter file in which to save the key (/Users/you/.ssh/id_ed25519):
```

Press **Enter** to accept the default location.

```
Enter passphrase (empty for no passphrase):
```

> [!TIP]
> Enter a strong passphrase. macOS Keychain will store it so you don't have to enter it every time.

## Step 3: Configure SSH Config

macOS can automatically load keys and store passphrases in Keychain. Create or edit your SSH config file:

### Check if config exists

```bash
cat ~/.ssh/config
```

### Create or edit the config

```bash
touch ~/.ssh/config
open -e ~/.ssh/config
```

Add the following configuration:

```ssh-config
Host github.com
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519
```

**Configuration explained:**

| Option | Purpose |
|--------|---------|
| `Host github.com` | Apply settings only to GitHub connections |
| `AddKeysToAgent yes` | Automatically add key to ssh-agent |
| `UseKeychain yes` | Store passphrase in macOS Keychain |
| `IdentityFile` | Path to your private key |

> [!NOTE]
> If you chose not to add a passphrase, omit the `UseKeychain yes` line.

## Step 4: Add Key to SSH Agent

Start the SSH agent in the background:

```bash
eval "$(ssh-agent -s)"
```

Add your key with Keychain integration:

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

> [!NOTE]
> On macOS versions before Monterey (12.0), use `-K` instead of `--apple-use-keychain`.

If you set a passphrase, enter it when prompted. It will be stored in Keychain.

## Step 5: Copy Public Key

Copy your public key to the clipboard:

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

Alternatively, display and manually copy:

```bash
cat ~/.ssh/id_ed25519.pub
```

The key looks like:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... your_email@example.com
```

## Step 6: Add Key to GitHub

1. Go to [GitHub SSH Settings](https://github.com/settings/keys)
2. Click **New SSH key**
3. Fill in the fields:

   | Field | Value |
   |-------|-------|
   | Title | Descriptive name (e.g., "MacBook Pro - Personal") |
   | Key type | Authentication Key |
   | Key | Paste your public key (already in clipboard) |

4. Click **Add SSH key**
5. Confirm with your GitHub password if prompted

## Step 7: Test Connection

Verify your SSH connection to GitHub:

```bash
ssh -T git@github.com
```

First-time connection prompt:

```
The authenticity of host 'github.com (IP)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Type `yes` and press Enter.

**Success message:**

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `Bad configuration option: usekeychain` | Add `IgnoreUnknown UseKeychain` before `UseKeychain yes` |
| `ssh-add: illegal option -- apple-use-keychain` | Use `-K` flag instead (older macOS) |
| Passphrase asked every time | Ensure `~/.ssh/config` has `UseKeychain yes` |

For more issues, see the [Troubleshooting Guide](../troubleshooting.md).

---

**Next Steps:**
- [Configure SSH for multiple accounts →](../ssh-config-guide.md)
- [Security best practices →](../security-best-practices.md)
