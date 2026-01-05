# SSH Key Setup for Windows

Complete guide for setting up SSH key authentication on Windows using PowerShell, Git Bash, or Windows Terminal.

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

- Windows 10 (version 1809+) or Windows 11
- One of the following:
  - **PowerShell** (built-in)
  - **Git Bash** (comes with [Git for Windows](https://git-scm.com/download/win))
  - **Windows Terminal** (recommended, from Microsoft Store)

## Step 1: Open Terminal

Choose one of these options:

| Terminal | How to Open |
|----------|-------------|
| PowerShell | Press `Win + X`, select "Windows PowerShell" |
| Git Bash | Right-click in folder → "Git Bash Here" |
| Windows Terminal | Press `Win + R`, type `wt`, press Enter |

## Step 2: Generate SSH Key

Run the following command, replacing the email with your GitHub email:

```powershell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

When prompted:

```
Enter file in which to save the key (C:\Users\YourName\.ssh\id_ed25519):
```

Press **Enter** to accept the default location.

```
Enter passphrase (empty for no passphrase):
```

> [!TIP]
> Enter a strong passphrase for added security. You can leave it empty, but a passphrase is recommended.

## Step 3: Start the SSH Agent

### Option A: PowerShell (Administrator)

Open PowerShell as Administrator and run:

```powershell
# Check if ssh-agent service exists
Get-Service ssh-agent

# Set the service to start automatically
Set-Service -Name ssh-agent -StartupType Automatic

# Start the service
Start-Service ssh-agent
```

### Option B: Git Bash

```bash
eval "$(ssh-agent -s)"
```

Expected output:

```
Agent pid 12345
```

## Step 4: Add Key to SSH Agent

Add your private key to the SSH agent:

**PowerShell:**

```powershell
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

**Git Bash:**

```bash
ssh-add ~/.ssh/id_ed25519
```

If you set a passphrase, you'll be prompted to enter it.

## Step 5: Copy Public Key

Copy your public key to the clipboard:

**PowerShell:**

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard
```

**Git Bash:**

```bash
clip < ~/.ssh/id_ed25519.pub
```

**Manual Method:**

Open the file and copy its contents:

```powershell
notepad $env:USERPROFILE\.ssh\id_ed25519.pub
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
   | Title | Descriptive name (e.g., "Work Laptop - Windows") |
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
| `ssh-agent` service not found | Install OpenSSH Client via Windows Settings → Apps → Optional Features |
| Permission denied | Ensure your public key is added to GitHub |
| Key not being used | Run `ssh-add -l` to check if key is loaded |

For more issues, see the [Troubleshooting Guide](../troubleshooting.md).

---

**Next Steps:**
- [Configure SSH for multiple accounts →](../ssh-config-guide.md)
- [Security best practices →](../security-best-practices.md)
