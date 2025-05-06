# 🔐 Managing Multiple GitHub Accounts with SSH (Official + Personal)

This guide walks you through setting up **two separate GitHub accounts** on the same machine using distinct SSH keys and a custom SSH configuration.

---

## 👤 Account Overview

- **Official GitHub**: https://github.com/lukman-official
  - SSH key: `~/.ssh/id_rsa` (assumed already set up)
- **Personal GitHub**: https://github.com/engr-lukman
  - Will use a new SSH key: `~/.ssh/id_ed25519_personal`

---

## ✅ Step 1: Generate SSH Key for Personal Account

```bash
ssh-keygen -t ed25519 -C "engr.lukman@gmail.com"
```

When prompted:

```bash
Enter file in which to save the key: ~/.ssh/id_ed25519_personal
```

Leave the passphrase empty (or set one if preferred).

## ✅ Step 2: Add the Key to the SSH Agent

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_personal
```

## ✅ Step 3: Add Public Key to Personal GitHub Account

Print your public key:

```bash
cat ~/.ssh/id_ed25519_personal.pub
```

Go to: https://github.com/settings/keys (logged into personal account)

Click "New SSH key", give it a title (e.g., Personal Laptop), and paste the copied key.

## ✅ Step 4: Configure SSH for Multiple Accounts

Edit your SSH config file:

```bash
nano ~/.ssh/config
```

Paste the following:

```ssh
# Official GitHub Account (Default)
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes

# Personal GitHub Account (using port 443 to bypass firewalls)
Host github-personal
  HostName ssh.github.com
  User git
  Port 443
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes
```

Save and close the file.

## ✅ Step 5: Clone or Update Personal Repositories

Clone using personal alias:

```bash
git clone git@github-personal:engr-lukman/engr-lukman.github.io.git
```

Or update an existing remote:

```bash
cd engr-lukman.github.io
git remote set-url origin git@github-personal:engr-lukman/engr-lukman.github.io.git
```

## ✅ Step 6: Test the SSH Connection

```bash
ssh -T git@github-personal
```

You should see:

```
Hi engr-lukman! You've successfully authenticated, but GitHub does not provide shell access.
```

If you get a port 22 error, double-check your SSH config is using Port 443.

## ✅ Step 7: Set Git Identity Per Repository

Inside your personal repository:

```bash
git config user.name "Lukman"
git config user.email "engr.lukman@gmail.com"
```

Inside your official repository (if needed):

```bash
git config user.name "Your Official Name"
git config user.email "your.official@company.com"
```

## ✅ Done!

You can now work with multiple GitHub accounts seamlessly using SSH keys and host aliases.

- Use github.com for official account.
- Use github-personal for personal account.
