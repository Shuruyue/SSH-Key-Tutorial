# SSH Security Best Practices

Guidelines for secure SSH key management and lifecycle practices.

## Table of Contents

- [Key Generation Best Practices](#key-generation-best-practices)
- [Passphrase Protection](#passphrase-protection)
- [Private Key Security](#private-key-security)
- [Key Rotation](#key-rotation)
- [Auditing and Monitoring](#auditing-and-monitoring)
- [Incident Response](#incident-response)
- [Quick Security Checklist](#quick-security-checklist)

## Key Generation Best Practices

### Use Ed25519 Algorithm

Ed25519 is the recommended algorithm for SSH keys:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

| Algorithm | Recommendation | Reason |
|-----------|----------------|--------|
| **Ed25519** | ✅ Recommended | Fastest, most secure, smallest key size |
| RSA 4096 | ✅ Acceptable | Good fallback for legacy systems |
| RSA 2048 | ⚠️ Minimum | Acceptable but prefer 4096 |
| DSA | ❌ Avoid | Deprecated by GitHub (March 2022) |
| ECDSA | ⚠️ Avoid | Potential implementation vulnerabilities |

### Avoid Weak Keys

```bash
# ❌ Bad: Default RSA (may be too small)
ssh-keygen

# ✅ Good: Ed25519
ssh-keygen -t ed25519 -C "identifier"

# ✅ Good: RSA with 4096 bits (if Ed25519 not supported)
ssh-keygen -t rsa -b 4096 -C "identifier"
```

## Passphrase Protection

### Always Use a Passphrase

A passphrase encrypts your private key, adding a second layer of protection:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# Enter a strong passphrase when prompted
```

> [!CAUTION]
> Without a passphrase, anyone who obtains your private key file can impersonate you.

### Strong Passphrase Guidelines

| Criteria | Recommendation |
|----------|----------------|
| Length | 15+ characters |
| Complexity | Mix of letters, numbers, symbols |
| Uniqueness | Don't reuse passwords |
| Memorability | Use a passphrase (sentence) over password |

**Example strong passphrases:**

```
correct-horse-battery-staple-2024
My$ecureGitHub_Key!ForWork
```

### Use SSH Agent

Avoid typing your passphrase repeatedly:

```bash
# Start agent
eval "$(ssh-agent -s)"

# Add key (enter passphrase once)
ssh-add ~/.ssh/id_ed25519
```

## Private Key Security

### Never Share Your Private Key

| File | Share? | Purpose |
|------|--------|---------|
| `id_ed25519` | ❌ NEVER | Your private key (secret) |
| `id_ed25519.pub` | ✅ Yes | Your public key (can be shared) |

### Secure File Permissions

```bash
# Set correct permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/config
```

### Don't Store Keys in Unsafe Locations

❌ **Avoid:**
- Cloud storage (Dropbox, Google Drive, OneDrive)
- Email
- Messaging apps
- Version control (Git repositories)
- Shared network drives

✅ **Safe locations:**
- Local `~/.ssh` directory with proper permissions
- Encrypted backup drives
- Hardware security keys (YubiKey)

### Use Separate Keys per Device

Generate unique keys for each device:

```bash
# Work laptop
ssh-keygen -t ed25519 -C "work-laptop" -f ~/.ssh/id_ed25519_work_laptop

# Home desktop
ssh-keygen -t ed25519 -C "home-desktop" -f ~/.ssh/id_ed25519_home_desktop
```

Benefits:
- Revoke individual devices without affecting others
- Track which device accessed what
- Limit blast radius if one key is compromised

## Key Rotation

### When to Rotate Keys

| Scenario | Action |
|----------|--------|
| Suspected compromise | Rotate immediately |
| Employee departure | Remove their keys |
| Regular schedule | Every 6-12 months |
| Device change | Generate new key for new device |

### How to Rotate Keys

1. **Generate new key:**

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com-2024"
   ```

2. **Add new key to GitHub:**

   Go to [GitHub SSH Settings](https://github.com/settings/keys) → New SSH key

3. **Test new key:**

   ```bash
   ssh -T git@github.com
   ```

4. **Remove old key from GitHub:**

   Delete the old key from GitHub SSH settings

5. **Delete old key from device:**

   ```bash
   rm ~/.ssh/id_ed25519_old
   rm ~/.ssh/id_ed25519_old.pub
   ```

## Auditing and Monitoring

### Review Authorized Keys

Periodically check which keys have access:

**On GitHub:**
1. Go to [SSH and GPG keys](https://github.com/settings/keys)
2. Review all keys
3. Remove any you don't recognize

**Check last used date:**
GitHub shows when each key was last used.

### Monitor for Unauthorized Access

**Check GitHub Security Log:**
1. Go to [Security Log](https://github.com/settings/security-log)
2. Filter by SSH events
3. Look for unfamiliar activities

### Verify Key Fingerprints

Compare fingerprints to ensure the right keys are in use:

```bash
# View your key's fingerprint
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

## Incident Response

### If Your Key Is Compromised

1. **Immediately remove the key from GitHub:**

   Go to [SSH Settings](https://github.com/settings/keys) → Delete key

2. **Generate a new key:**

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519_new
   ```

3. **Delete the compromised key:**

   ```bash
   rm ~/.ssh/id_ed25519_compromised
   rm ~/.ssh/id_ed25519_compromised.pub
   ```

4. **Review access logs:**

   Check [GitHub Security Log](https://github.com/settings/security-log) for suspicious activity

5. **Rotate passwords:**

   Change your GitHub password if in doubt

6. **Update SSH config:**

   Point to the new key in `~/.ssh/config`

### Signs of Compromise

| Warning Sign | Action |
|--------------|--------|
| Unknown SSH keys in GitHub | Remove immediately |
| Unexpected commits in your repos | Investigate and revoke access |
| Security alerts from GitHub | Follow GitHub's instructions |
| "Last used" shows unfamiliar times | Rotate keys immediately |

## Quick Security Checklist

Use this checklist to ensure your SSH setup is secure:

- [ ] Using Ed25519 algorithm
- [ ] Passphrase set on private key
- [ ] Key permissions are `600` (private) / `644` (public)
- [ ] Separate keys per device
- [ ] Keys added only to devices you control
- [ ] Unused keys removed from GitHub
- [ ] `~/.ssh` directory permissions are `700`
- [ ] Private keys not stored in cloud storage
- [ ] Regular key rotation schedule in place

---

**Related Guides:**
- [SSH Config Guide →](ssh-config-guide.md)
- [Troubleshooting →](troubleshooting.md)
