# Getting Started with SSH Key Authentication

SSH (Secure Shell) keys provide a secure method of authenticating to remote servers and services like GitHub without using passwords.

## Table of Contents

- [What is SSH Key Authentication?](#what-is-ssh-key-authentication)
- [How It Works](#how-it-works)
- [Algorithm Selection](#algorithm-selection)
- [General Workflow](#general-workflow)
- [Choose Your Platform](#choose-your-platform)

## What is SSH Key Authentication?

SSH key authentication uses a pair of cryptographic keys:

| Key Type | Location | Purpose |
|----------|----------|---------|
| **Private Key** | Your computer (`~/.ssh/id_ed25519`) | Proves your identity (never share!) |
| **Public Key** | Remote server/GitHub (`~/.ssh/id_ed25519.pub`) | Verifies your private key |

When you connect to GitHub, your private key creates a signature that only your public key can verify — proving you are who you claim to be.

## How It Works

```
┌─────────────────┐                      ┌─────────────────┐
│   Your Computer │                      │     GitHub      │
│                 │                      │                 │
│  ┌───────────┐  │    1. Connect        │  ┌───────────┐  │
│  │ Private   │  │ ─────────────────►   │  │ Public    │  │
│  │ Key       │  │                      │  │ Key       │  │
│  └───────────┘  │    2. Challenge      │  └───────────┘  │
│       │         │ ◄─────────────────   │       │         │
│       ▼         │                      │       ▼         │
│  Sign with      │    3. Signature      │  Verify with    │
│  Private Key    │ ─────────────────►   │  Public Key     │
│                 │                      │                 │
│                 │    4. Access OK      │                 │
│                 │ ◄─────────────────   │                 │
└─────────────────┘                      └─────────────────┘
```

## Algorithm Selection

### Recommended: Ed25519

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

| Feature | Ed25519 | RSA (4096-bit) |
|---------|---------|----------------|
| Key Size | 256 bits | 4096 bits |
| Security | Excellent | Excellent |
| Performance | Faster | Slower |
| Compatibility | Modern systems | Universal |

### Fallback: RSA

Use RSA only if your system doesn't support Ed25519 (older systems):

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

> [!NOTE]
> GitHub dropped support for DSA keys and RSA keys with SHA-1 signatures as of March 2022.

## General Workflow

Setting up SSH keys follows these steps on any platform:

1. **Generate** a new SSH key pair
2. **Start** the SSH agent
3. **Add** your private key to the agent
4. **Copy** your public key
5. **Add** the public key to GitHub
6. **Test** your connection

## Choose Your Platform

Select your operating system for detailed instructions:

| Platform | Guide |
|----------|-------|
| Windows | [Windows Setup Guide](cross-platform-setup/windows.md) |
| macOS | [macOS Setup Guide](cross-platform-setup/macos.md) |
| Linux | [Linux Setup Guide](cross-platform-setup/linux.md) |

---

**Next Steps:**
- [Configure your SSH config file →](ssh-config-guide.md)
- [Security best practices →](security-best-practices.md)
