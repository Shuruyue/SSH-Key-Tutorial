# SSH Config Guide

The `~/.ssh/config` file allows you to customize SSH connections with aliases, default options, and key management. This guide covers essential configurations for GitHub and advanced use cases.

## Table of Contents

- [Why Use SSH Config?](#why-use-ssh-config)
- [Basic Configuration](#basic-configuration)
- [Managing Multiple GitHub Accounts](#managing-multiple-github-accounts)
- [Custom Host Aliases](#custom-host-aliases)
- [Advanced Options](#advanced-options)
- [Complete Example](#complete-example)

## Why Use SSH Config?

| Benefit | Description |
|---------|-------------|
| **Convenience** | Avoid typing long commands with custom aliases |
| **Multiple Accounts** | Use different keys for personal and work GitHub accounts |
| **Automatic Key Selection** | SSH automatically uses the correct key for each host |
| **Persistent Settings** | Configuration survives reboots |

## Basic Configuration

### Create the Config File

```bash
# Check if file exists
cat ~/.ssh/config

# Create if it doesn't exist
touch ~/.ssh/config
chmod 600 ~/.ssh/config
```

### Minimal GitHub Configuration

```ssh-config
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    AddKeysToAgent yes
```

**Options explained:**

| Option | Purpose |
|--------|---------|
| `Host` | Alias used to match this configuration |
| `HostName` | Actual hostname to connect to |
| `User` | Username for connection (always `git` for GitHub) |
| `IdentityFile` | Path to your private key |
| `AddKeysToAgent` | Automatically add key to ssh-agent |

## Managing Multiple GitHub Accounts

Use separate SSH keys for personal and work accounts.

### Step 1: Generate Separate Keys

```bash
# Personal account
ssh-keygen -t ed25519 -C "personal@email.com" -f ~/.ssh/id_ed25519_personal

# Work account
ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work
```

### Step 2: Configure SSH Config

```ssh-config
# Personal GitHub account
Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    IdentitiesOnly yes

# Work GitHub account
Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes
```

### Step 3: Add Keys to GitHub

Add each public key to the corresponding GitHub account:
- `~/.ssh/id_ed25519_personal.pub` → Personal account
- `~/.ssh/id_ed25519_work.pub` → Work account

### Step 4: Clone with Correct Host

```bash
# Personal repositories
git clone git@github.com-personal:username/repo.git

# Work repositories
git clone git@github.com-work:company/repo.git
```

### Step 5: Set Git Config per Repository

```bash
cd work-repo
git config user.email "work@company.com"
git config user.name "Your Name"
```

## Custom Host Aliases

Create short aliases for frequently accessed hosts:

```ssh-config
# Short alias for GitHub
Host gh
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519

# GitLab with custom port
Host gl
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519_gitlab
    Port 22

# Private server
Host myserver
    HostName 192.168.1.100
    User admin
    IdentityFile ~/.ssh/id_ed25519_server
    Port 2222
```

Usage:

```bash
git clone gh:username/repo.git
ssh myserver
```

## Advanced Options

### IdentitiesOnly

Prevents SSH from trying all keys in ssh-agent:

```ssh-config
Host github.com
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```

### Proxy Jump (Bastion Host)

Connect through an intermediate server:

```ssh-config
Host internal-server
    HostName 10.0.0.5
    User admin
    ProxyJump bastion.company.com
```

### Connection Keep-Alive

Prevent connection timeouts:

```ssh-config
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

### macOS Keychain Integration

For macOS users:

```ssh-config
Host github.com
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519
```

> [!NOTE]
> Add `IgnoreUnknown UseKeychain` before `UseKeychain yes` if you get errors.

## Complete Example

Here's a comprehensive configuration file:

```ssh-config
# Global settings
Host *
    AddKeysToAgent yes
    IdentitiesOnly yes
    ServerAliveInterval 60

# Personal GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519

# Work GitHub (separate account)
Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work

# GitLab
Host gitlab.com
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519

# Bitbucket
Host bitbucket.org
    HostName bitbucket.org
    User git
    IdentityFile ~/.ssh/id_ed25519
```

---

## Troubleshooting

Test which key is being used:

```bash
ssh -vT git@github.com
```

Look for lines like:

```
debug1: Offering public key: /home/user/.ssh/id_ed25519
```

For more help, see the [Troubleshooting Guide](troubleshooting.md).

---

**Next Steps:**
- [Security best practices →](security-best-practices.md)
- [Platform-specific setup →](cross-platform-setup/windows.md)
