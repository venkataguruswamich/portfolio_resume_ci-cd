# 🚀 Host Your Resume on AWS EC2 with CI/CD Using GitHub Actions

This project demonstrates how to automatically deploy your personal resume website to an AWS EC2 instance using GitHub Actions.

Whenever you push updates to GitHub, your website is automatically deployed to EC2.

---

# 📌 Project Architecture

```text
Local System → GitHub Repo → GitHub Actions → AWS EC2 → Apache Web Server → Live Resume
```

---

# 📁 Project Structure

```text
portfolio-resume-cicd/
│── index.html
│── style.css
│── images/
│── README.md
└── .github/
    └── workflows/
        └── github-actions-ec2.yml
```

---

# 🧰 Prerequisites

Before starting, make sure you have:

* AWS Account
* GitHub Account
* Resume website files (HTML/CSS)
* Git installed on Windows
* Basic Linux command knowledge

---

# 🪜 Step 1: Create AWS EC2 Instance

## 1. Login to AWS Console

Open AWS Console and go to EC2 Dashboard.

## 2. Launch Instance

Click **Launch Instance**

## 3. Configure Instance

Use these settings:

* Name: `resume-server`
* AMI: Ubuntu Server 24.04 LTS
* Instance Type: `t2.micro`
* Key Pair: Create new key pair

Download `.pem` file and keep safe.

## 4. Security Group Rules

Allow:

* SSH (Port 22) → My IP
* HTTP (Port 80) → Anywhere
* HTTPS (Port 443) → Anywhere

## 5. Launch Instance

Click **Launch Instance**

---

# 🪜 Step 2: Connect to EC2

Use terminal / Git Bash:

```bash
chmod 400 your-key.pem
ssh -i your-key.pem ubuntu@YOUR_PUBLIC_IP
```

Example:

```bash
ssh -i resume.pem ubuntu@13.xx.xx.xx
```

---

# 🪜 Step 3: Test Manual Setup (Optional)

Install Apache manually once:

```bash
sudo apt update
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
```

Open browser:

```text
http://YOUR_PUBLIC_IP
```

Apache page should load.

---

# 🪜 Step 4: Create GitHub Repository

Go to GitHub → New Repository

## Recommended Name

```text
portfolio-resume-cicd
```

## Settings

* Public Repository
* Add README.md

Click **Create Repository**

---

# 🪜 Step 5: Upload Resume Files

Open terminal in project folder:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/portfolio-resume-cicd.git
git push -u origin main
```

---

# 🪜 Step 6: Create GitHub Secrets

Open your repository:

```text
Settings → Secrets and variables → Actions
```

Click **New repository secret**

Create these secrets one by one.

---

## Secret 1

Name:

```text
EC2_SSH_KEY
```

Value:

Paste full `.pem` file content:

```text
-----BEGIN OPENSSH PRIVATE KEY-----
xxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxx
-----END OPENSSH PRIVATE KEY-----
```

---

## Secret 2

Name:

```text
HOST_DNS
```

Value:

Your EC2 Public DNS:

```text
ec2-xx-xx-xx-xx.ap-south-1.compute.amazonaws.com
```

---

## Secret 3

Name:

```text
USERNAME
```

Value:

```text
ubuntu
```

(Use `ec2-user` for Amazon Linux)

---

## Secret 4

Name:

```text
TARGET_DIR
```

Value:

```text
/var/www/html
```

---

# 🪜 Step 7: Create GitHub Actions Workflow

Inside repo create folders:

```text
.github/workflows/
```

Inside workflows create file:

```text
github-actions-ec2.yml
```

Final path:

```text
.github/workflows/github-actions-ec2.yml
```

---

# 🪜 Step 8: Add Workflow Code

Paste below code inside `github-actions-ec2.yml`

```yaml
name: Push-to-EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    name: Deploy to EC2 on main branch push
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the files
        uses: actions/checkout@v2

      - name: Deploy files to EC2
        uses: easingthemes/ssh-deploy@main
        env:
          SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
          REMOTE_HOST: ${{ secrets.HOST_DNS }}
          REMOTE_USER: ${{ secrets.USERNAME }}
          TARGET: ${{ secrets.TARGET_DIR }}

      - name: Install Apache and Move Files
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST_DNS }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            sudo apt-get update -y
            sudo apt-get install apache2 -y
            sudo systemctl start apache2
            sudo systemctl enable apache2
            sudo cp -r ${{ secrets.TARGET_DIR }}/* /var/www/html/
```

---

# 🪜 Step 9: Commit Workflow File

Inside GitHub:

Commit message:

```text
Added GitHub Actions deployment workflow
```

Click:

```text
Commit changes
```

---

# 🪜 Step 10: Trigger Deployment

Make any change locally:

```bash
git add .
git commit -m "Updated resume content"
git push
```

This automatically starts deployment.

---

# 🪜 Step 11: Check Workflow Status

Open:

```text
GitHub Repo → Actions Tab
```

You can see:

* Running
* Success
* Failed logs

---

# 🪜 Step 12: Open Live Website

Open browser:

```text
http://YOUR_PUBLIC_IP
```

Your resume is now live.

---

# 🔄 Future Updates

Whenever you update your resume:

```bash
git add .
git commit -m "Updated skills"
git push
```

Website auto updates.

---

# 🛠️ Troubleshooting

---

## SSH Permission Denied

Check secret key format.

Make sure full `.pem` content copied.

---

## Apache Not Running

SSH to EC2:

```bash
sudo systemctl restart apache2
sudo systemctl status apache2
```

---

## GitHub Action Failed

Check:

* Correct DNS
* Correct username
* Correct PEM key
* Port 22 open in Security Group

---

# 📌 Useful Commands

## Login EC2

```bash
ssh -i your-key.pem ubuntu@YOUR_PUBLIC_IP
```

## Restart Apache

```bash
sudo systemctl restart apache2
```

## Check Website Files

```bash
ls -la /var/www/html
```

---

# 🎯 Final Result

You now have:

✅ AWS EC2 Resume Hosting
✅ GitHub Repository
✅ GitHub Actions CI/CD Pipeline
✅ Automatic Deployment on Push
✅ Live Resume Website

---

# 👨‍💻 Author

Your Name

GitHub: [https://github.com/venkataguruswamich](https://github.com/venkataguruswamich)
