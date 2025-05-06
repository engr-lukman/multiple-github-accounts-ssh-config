# Managing Multiple GitHub Accounts with SSH

> **Working with both official and personal GitHub accounts on one machine? This guide is for you!**

This step-by-step guide helps you set up and manage two GitHub accounts on the same computer, without constantly switching credentials.

## What You'll Achieve

- Use different SSH keys for different GitHub accounts
- Seamlessly switch between accounts without password prompts
- Keep your official and personal projects completely separate

## Account Setup Overview

| Account | GitHub URL | SSH Key |
|---------|------------|---------|
| **Official** | github.com/lukman-official | ~/.ssh/id_rsa (default) |
| **Personal** | github.com/engr-lukman | ~/.ssh/id_ed25519_personal (new) |

## Setup Guide

### Step-01: Generate a New SSH Key for Your Personal Account

```bash
ssh-keygen -t ed25519 -C "engr.lukman@gmail.com"
```

When prompted:
- Enter: `~/.ssh/id_ed25519_personal` for the file location
- Set a passphrase (recommended) or leave empty

### Step-02: Add Your New Key to SSH Agent

```bash
# Start the SSH agent
eval "$(ssh-agent -s)"

# Add your personal key
ssh-add ~/.ssh/id_ed25519_personal
```

### Step-03: Add Key to Your Personal GitHub Account

1. Copy your public key:
   ```bash
   cat ~/.ssh/id_ed25519_personal.pub
   ```

2. Add to GitHub:
   - Go to [GitHub SSH Settings](https://github.com/settings/keys) (while logged into your personal account)
   - Click "New SSH key"
   - Add a title (e.g., "My Development Machine")
   - Paste the key content
   - Click "Add SSH key"

### Step-04: Create SSH Config for Multiple Accounts

Edit your SSH config file:

```bash
nano ~/.ssh/config
```

Add these configurations:

```
# Default (Official) GitHub Account
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes

# Personal GitHub Account
Host github-personal
  HostName ssh.github.com
  User git
  Port 443  # Uses port 443 to bypass potential firewalls
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes
```

### Step-05: Work with Your Personal Repositories

**To clone a new repo:**
```bash
git clone git@github-personal:engr-lukman/your-repo-name.git
```

**To update an existing repo:**
```bash
cd your-repo-directory
git remote set-url origin git@github-personal:engr-lukman/your-repo-name.git
```

### Step-06: Test Your Configuration

Verify the connection works:

```bash
ssh -T git@github-personal
```

You should see: `Hi engr-lukman! You've successfully authenticated, but GitHub does not provide shell access.`

### Step-07: Set Repository-Specific Git Identity

**For personal projects:**
```bash
cd your-personal-repo
git config user.name "Lukman"
git config user.email "engr.lukman@gmail.com"
```

**For official projects:**
```bash
cd your-official-repo
git config user.name "Your Official Name"
git config user.email "your.official@company.com"
```

## Quick Reference

- For **official** repositories: Use `github.com` (default)
- For **personal** repositories: Use `github-personal`

## Troubleshooting

- **Connection error on port 22?** Make sure your SSH config uses Port 443 for the personal account
- **Permission denied?** Check that you've added the correct SSH key to the correct GitHub account
- **Wrong identity used?** Verify your repository's remote URL is using the correct host alias

---

*Last updated: May 6, 2025*
