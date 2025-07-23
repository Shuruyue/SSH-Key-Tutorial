# SSH Key Setup Instructions for GitHub

## Step 1: Generate SSH Key (ed25519)

Open a terminal (Git Bash, PowerShell, or Command Prompt), and execute the following command:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

When prompted:
- **File location**: Press `Enter` to accept the default path (`C:\Users\YourName\.ssh\id_ed25519`)
- **Passphrase**: Optional. You may leave it blank or set a secure passphrase for added protection.

> Note: The `ed25519` algorithm is recommended for its improved security and performance over RSA.

---

## Step 2: View and Copy the Public Key

To copy your SSH public key, open it using:

```bash
notepad C:\Users\YourName\.ssh\id_ed25519.pub
```

Copy the entire contents of the file. It should resemble the following format:

```
ssh-ed25519 AAAAC3Nza... your-email@example.com
```

---

## Step 3: Add SSH Key to GitHub

1. Navigate to [GitHub SSH Settings](https://github.com/settings/keys)
2. Click **New SSH key**
3. **Title**: Assign a recognizable label (e.g., `"Work Laptop"` or `"Home Desktop"`)
4. **Key type**: Leave as **Authentication Key**
5. **Key**: Paste the copied public key into the provided field
6. Click **Add SSH key** to finalize

> Tip: Use different key names per device for better traceability.

---

## Verification (Optional)

You can test your connection to GitHub using:

```bash
ssh -T git@github.com
```

Expected response:

```
Hi your-username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## Additional Notes

- Never share your **private key** (`id_ed25519`) with anyone.
- For managing multiple keys, consider using an SSH config file (`~/.ssh/config`).
- This setup allows secure Git operations (clone, push, pull) without entering your GitHub credentials every time.
