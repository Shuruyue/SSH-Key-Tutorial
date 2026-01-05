# SSH Key Setup for Linux

Complete guide for setting up SSH key authentication on Linux distributions (Ubuntu, Debian, Fedora, Arch, etc.).

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Open Terminal](#step-1-open-terminal)
- [Step 2: Generate SSH Key](#step-2-generate-ssh-key)
- [Step 3: Start the SSH Agent](#step-3-start-the-ssh-agent)
- [Step 4: Add Key to SSH Agent](#step-4-add-key-to-ssh-agent)
- [Step 5: Copy Public Key](#step-5-copy-public-key)
- [Step 6: Add Key to GitHub](#step-6-add-key-to-github)
- [Step 7: Test Connection](#step-7-test-connection)

## Prerequisites

- A Linux distribution with OpenSSH installed
- Terminal access

Most distributions come with OpenSSH pre-installed. If not:

**Ubuntu/Debian:**

```bash
sudo apt update && sudo apt install openssh-client
```

**Fedora:**

```bash
sudo dnf install openssh-clients
```

**Arch Linux:**

```bash
sudo pacman -S openssh
```

## Step 1: Open Terminal

Open your terminal emulator:

- Press `Ctrl + Alt + T` (most distributions)
- Or search for "Terminal" in your application menu

## Step 2: Generate SSH Key

Run the following command, replacing the email with your GitHub email:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

When prompted:

```
Enter file in which to save the key (/home/you/.ssh/id_ed25519):
```

Press **Enter** to accept the default location.

```
Enter passphrase (empty for no passphrase):
```

> [!TIP]
> Enter a strong passphrase for added security. Consider using a keyring manager to avoid entering it repeatedly.

Expected output:

```
Your identification has been saved in /home/you/.ssh/id_ed25519
Your public key has been saved in /home/you/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:abcd1234... your_email@example.com
```

## Step 3: Start the SSH Agent

Start the SSH agent in the background:

```bash
eval "$(ssh-agent -s)"
```

Expected output:

```
Agent pid 12345
```

### Automatic Agent Startup (Optional)

To start ssh-agent automatically when you log in, add to your `~/.bashrc` or `~/.zshrc`:

```bash
# Start ssh-agent if not running
if [ -z "$SSH_AUTH_SOCK" ]; then
    eval "$(ssh-agent -s)" > /dev/null
fi
```

## Step 4: Add Key to SSH Agent

Add your private key to the SSH agent:

```bash
ssh-add ~/.ssh/id_ed25519
```

If you set a passphrase, you'll be prompted to enter it.

Verify the key was added:

```bash
ssh-add -l
```

## Step 5: Copy Public Key

### Option A: Using xclip

Install xclip if needed:

```bash
# Ubuntu/Debian
sudo apt install xclip

# Fedora
sudo dnf install xclip

# Arch
sudo pacman -S xclip
```

Copy to clipboard:

```bash
xclip -selection clipboard < ~/.ssh/id_ed25519.pub
```

### Option B: Using xsel

```bash
xsel --clipboard < ~/.ssh/id_ed25519.pub
```

### Option C: Display and Copy Manually

```bash
cat ~/.ssh/id_ed25519.pub
```

Select and copy the output. The key looks like:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... your_email@example.com
```

## Step 6: Add Key to GitHub

1. Go to [GitHub SSH Settings](https://github.com/settings/keys)
2. Click **New SSH key**
3. Fill in the fields:

   | Field | Value |
   |-------|-------|
   | Title | Descriptive name (e.g., "Linux Desktop - Ubuntu") |
   | Key type | Authentication Key |
   | Key | Paste your public key |

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
| `Could not open connection to authentication agent` | Run `eval "$(ssh-agent -s)"` first |
| Permission denied | Check key permissions: `chmod 600 ~/.ssh/id_ed25519` |
| ssh-agent dies after logout | Add agent startup to shell profile |

For more issues, see the [Troubleshooting Guide](../troubleshooting.md).

---

**Next Steps:**
- [Configure SSH for multiple accounts →](../ssh-config-guide.md)
- [Security best practices →](../security-best-practices.md)
