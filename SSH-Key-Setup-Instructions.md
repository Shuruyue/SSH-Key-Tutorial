# SSH Key Setup Instructions for GitHub

## Step 1: Generate SSH Key (ed25519)

Open your terminal and enter the following command:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

When prompted:
- **File location**: Press `Enter` to accept the default location (e.g., `C:\Users\YourName\.ssh\id_ed25519`)
- **Passphrase**: You can leave it empty or set one for added security

## Step 2: View and Copy the Public Key

Open the public key file:

```bash
notepad C:\Users\YourName\.ssh\id_ed25519.pub
```

Copy the entire line. It should look like:

```
ssh-ed25519 AAAAC3Nza... your-email@example.com
```

## Step 3: Add SSH Key to GitHub

1. Go to [GitHub SSH Keys](https://github.com/settings/keys)
2. Click on **New SSH key**
3. **Title**: Name your device (e.g., "My Laptop")
4. **Key type**: Keep it as "Authentication Key"
5. **Key**: Paste the public key you copied
6. Click **Add SSH key**

