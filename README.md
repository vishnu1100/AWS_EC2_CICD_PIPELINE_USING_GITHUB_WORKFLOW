
# 🚀 AWS EC2 CI/CD Pipeline Using GitHub Workflow

This project demonstrates how to **automate deployment to an AWS EC2 instance** using **GitHub Actions**. When you push code to the `main` branch, GitHub automatically SSHs into your EC2 server and pulls the latest changes — no manual login or SSH required!

---

## 🔧 Prerequisites

Before setting this up, ensure the following:

- ✅ You have an **EC2 instance** running (Ubuntu, Amazon Linux, etc.)
- ✅ Git is installed on the EC2 instance
- ✅ SSH key-based access to EC2 is enabled
- ✅ Your GitHub repository is already cloned on the EC2 instance
- ✅ You can `git pull` manually on EC2 (no 2FA issues)

---

## 🔐 GitHub Secrets Setup

Go to your repository → **Settings → Secrets → Actions** and add the following secrets:

| Secret Name     | Description |
|----------------|-------------|
| `EC2_HOST`      | Public IP of your EC2 instance (e.g., `54.12.34.56`) |
| `EC2_USER`      | SSH user (usually `ubuntu`, `ec2-user`, etc.) |
| `EC2_SSH_KEY`   | Your **private SSH key** (contents of `~/.ssh/id_rsa`) — use a key with access to the EC2 instance |

---

## 🛠 GitHub Workflow File

Create or edit the file:  
**`.github/workflows/aws.yml`**

Paste the following workflow:

```yaml
name: Deploy to EC2 on Push

on:
  push:
    branches:
      - main

jobs:
  deploy:
    name: SSH into EC2 and Pull Latest Code
    runs-on: ubuntu-latest

    steps:
      - name: Connect and Pull Code
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            cd /path/to/your/project
            git pull origin main
```

> 🔄 Replace `/path/to/your/project` with the actual path of your cloned project on the EC2 server.

---

## ✅ What Happens on Push

1. You push code to the `main` branch.
2. GitHub triggers this workflow.
3. It connects securely to your EC2 server.
4. Navigates to your project folder.
5. Runs `git pull` to get the latest code.

---

## 🎯 Benefits

- No need to SSH manually every time you make a change
- No Docker or ECS setup required
- Fast and lightweight CI/CD for simple projects
- Works great for monolithic Node.js, Python, PHP apps etc.

---

## 🚨 Security Tip

Keep your private SSH key safe.  
Use a **deploy-only SSH key** if possible, and restrict EC2 access by IP (via security group).
