# SSH Key Tutorial

A comprehensive, cross-platform guide for setting up SSH key authentication with GitHub.

## Table of Contents

- [About](#about)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [Resources](#resources)
- [License](#license)

## About

This repository provides production-ready instructions for implementing SSH key authentication with GitHub. It covers key generation using modern algorithms, SSH agent configuration, multi-account management, and security best practices.

SSH keys offer significant advantages over password authentication:

| Benefit | Description |
|---------|-------------|
| **Passwordless** | No credential prompts for Git operations |
| **Secure** | Cryptographically stronger than passwords |
| **Convenient** | Seamless authentication once configured |

## Prerequisites

Before you begin, ensure you have:

- Git installed on your system
- A GitHub account
- Terminal access (PowerShell, Terminal, Git Bash, etc.)

## Quick Start

Generate a new SSH key:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Start the SSH agent and add your key:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

Copy your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Add the key to [GitHub SSH Settings](https://github.com/settings/keys), then test the connection:

```bash
ssh -T git@github.com
```

For detailed platform-specific instructions, see the documentation below.

## Documentation

### Getting Started

| Document | Description |
|----------|-------------|
| [Getting Started](docs/getting-started.md) | SSH fundamentals, algorithm selection, workflow overview |

### Platform Guides

| Platform | Guide |
|----------|-------|
| Windows | [Windows Setup](docs/cross-platform-setup/windows.md) |
| macOS | [macOS Setup](docs/cross-platform-setup/macos.md) |
| Linux | [Linux Setup](docs/cross-platform-setup/linux.md) |

### Advanced Topics

| Document | Description |
|----------|-------------|
| [SSH Config Guide](docs/ssh-config-guide.md) | Multi-account setup, host aliases, advanced options |
| [Troubleshooting](docs/troubleshooting.md) | Common errors and solutions |
| [Security Best Practices](docs/security-best-practices.md) | Key lifecycle, rotation, incident response |

## Contributing

Contributions are welcome. To contribute:

1. Fork this repository
2. Create a feature branch
3. Commit your changes
4. Open a Pull Request

## Resources

- [GitHub SSH Documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [OpenSSH Manual](https://www.openssh.com/manual.html)

## License

This project is licensed under the MIT License.

---

**Next Steps:**
- [Getting Started with SSH Key Authentication](docs/getting-started.md)
- [Security Best Practices](docs/security-best-practices.md)
