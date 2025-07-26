# Complete Guide: Installing n8n Locally on Windows

*A step-by-step guide to get n8n (workflow automation) running on your local Windows machine - includes troubleshooting for common issues!*

## What You'll Get

By the end of this guide, you'll have:
- ✅ n8n running locally at `http://localhost:5678`
- ✅ A visual workflow builder for automation
- ✅ The ability to connect APIs, databases, Gmail, Slack, and more
- ✅ Your own self-hosted alternative to Zapier/IFTTT

## Prerequisites

- Windows 10 or 11
- Administrator access to your computer
- Stable internet connection
- About 30 minutes of time

## Step 1: Install Node.js via Chocolatey

### 1.1 Open Regular PowerShell
- Press `Windows Key + R`
- Type `powershell`
- Press Enter

### 1.2 Get Admin PowerShell Access
In the regular PowerShell window, run:
```powershell
Start-Process powershell -Verb RunAs
```
*This opens PowerShell with administrator privileges.*

**Note:** If right-clicking "Run as administrator" doesn't work, this method bypasses the issue!

### 1.3 Install Chocolatey
Copy and paste this entire command into your admin PowerShell:
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

Wait for installation to complete (you'll see "Chocolatey CLI (choco.exe) is now ready").

### 1.4 Close and Reopen Admin PowerShell
1. Close the current PowerShell window
2. Open regular PowerShell: `Windows Key + R` → `powershell` → Enter
3. Get admin access again: `Start-Process powershell -Verb RunAs`

### 1.5 Install Node.js
```powershell
choco install nodejs
```
When prompted "Do you want to run the script?", type `Y` and press Enter.

### 1.6 Refresh Environment
```powershell
Import-Module $env:ChocolateyInstall\helpers\chocolateyProfile.psm1
refreshenv
```

### 1.7 Verify Installation
```powershell
node --version
npm --version
```
You should see version numbers for both commands.

## Step 2: Install n8n

### 2.1 Install n8n Globally
```powershell
npm install n8n -g
```
This will take 10-15 minutes and install about 1,973 packages. You'll see many warnings - this is normal!

**Important:** Don't click in the console window while it's installing, as this can pause the process. If you accidentally click and see a cursor, just press Enter to resume.

## Step 3: Start n8n

### 3.1 Launch n8n
```powershell
n8n
```

### 3.2 Allow Firewall Access
Windows will show a security alert asking about network access:
- ✅ Check both "Private networks" and "Public networks"
- Click "Allow access"

### 3.3 Wait for Database Setup
You'll see many "migration" messages - this is n8n setting up its database. Wait until you see:
```
Editor is now accessible via:
http://localhost:5678

Press "o" to open in Browser.
```

## Step 4: Access n8n

### 4.1 Open in Browser
Either:
- Press `o` in PowerShell, OR
- Open your browser and go to `http://localhost:5678`

### 4.2 Create Your Account
Fill out the setup form:
- Email (use a real email)
- First Name
- Last Name  
- Password (8+ characters, 1 number, 1 capital letter)

### 4.3 Start Automating!
You'll see the n8n dashboard with options to:
- Create workflows from scratch
- Try AI agent examples
- Browse templates
- Connect services like Gmail, Slack, databases, etc.

## Common Issues & Solutions

### Issue: "npm is not recognized"
**Solution:** Node.js isn't installed. Follow Step 1 completely.

### Issue: PowerShell admin won't open when right-clicking
**Solution:** Use the `Start-Process powershell -Verb RunAs` method from Step 1.2.

### Issue: npm install seems frozen
**Solution:** You probably clicked in the console window. Press Enter to resume.

### Issue: Lots of warnings during npm install
**Solution:** This is normal! n8n has many dependencies. As long as you don't see "ERROR", you're fine.

### Issue: Windows Firewall blocks n8n
**Solution:** Click "Allow access" for both private and public networks.

### Issue: Can't access localhost:5678
**Solution:** Make sure n8n is still running in PowerShell and you've allowed firewall access.

## What is n8n?

n8n is a **visual workflow automation tool** that lets you:
- **Connect different services** (Gmail, Slack, databases, APIs, etc.)
- **Automate repetitive tasks** without coding
- **Create workflows visually** with drag-and-drop
- **Self-host for free** (vs. paying for Zapier)
- **Handle complex logic** when needed

Think of it as "IFTTT/Zapier but more powerful and running on your own computer."

## Next Steps

Once n8n is running, you can:
1. **Explore Templates** - Browse pre-built workflows
2. **Connect Your Services** - Link Gmail, Slack, databases
3. **Build Your First Workflow** - Start with something simple
4. **Learn the Interface** - Drag nodes, connect them, test workflows

## Stopping n8n

To stop n8n:
- Go back to your PowerShell window
- Press `Ctrl + C`
- Type `Y` to confirm

To start it again later:
- Open admin PowerShell (Step 1.2)
- Run `n8n`

## Why This Method?

This guide uses Chocolatey because:
- ✅ Handles all dependencies automatically
- ✅ Provides clean installation/uninstallation
- ✅ Avoids manual downloads and PATH issues
- ✅ Works around Windows admin elevation problems

---

**Created after an epic troubleshooting session involving PowerShell mysteries, dependency speculation, and accidental console pausing! 😄**

*Happy automating! 🚀*
