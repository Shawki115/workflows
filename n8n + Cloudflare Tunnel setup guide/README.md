# Self-Host n8n + Cloudflare Tunnel (Docker on Linux or Windows)

This repository documents how to self-host **n8n** securely behind **Cloudflare Tunnel** using **Docker**.
✅ Domain purchased and already connected to **Cloudflare** (e.g., `yourdomain.com`).

With this setup:
- You access n8n via HTTPS: `https://n8n.yourdomain.com`
- No inbound ports need to be opened on your router/firewall
- Cloudflare Tunnel provides a secure public entry point

---

## What You Need (Prerequisites)

### 1) Domain + Cloudflare
- A domain (example: `yourdomain.com`)
- Domain is **added to Cloudflare** and DNS is managed by Cloudflare ✅

### 2) A machine to run n8n (choose one)
- **Linux server (recommended for 24/7 uptime)**: Ubuntu 20.04/22.04/24.04
- **Windows PC** with **Docker Desktop** (works, but the PC must stay on)

### 3) Software
- Docker + Docker Compose
- Cloudflare Zero Trust account (for Tunnels)

---

## Step 1 — Choose Your Platform

### Option A: Linux (Recommended)
Best for stable hosting.

### Option B: Windows (Docker Desktop)
Good for testing or if you don’t have a server.

---

## Step 2 — Install Docker

### A) Install Docker on Ubuntu (Linux)
```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker $USER
newgrp docker

docker --version
docker compose version

B) Install Docker on Windows

Install Docker Desktop from the official Docker website

Enable WSL2 (Docker Desktop will guide you)

Open PowerShell/Terminal and verify:

docker --version
docker compose version

##Step 3 — Create Project Folder
Linux
mkdir -p ~/n8n-cloudflare
cd ~/n8n-cloudflare

Windows

Create a folder, for example:
C:\projects\n8n-cloudflare

Then open a terminal in that folder.

##Step 4 — Create .env (Configuration)

Create a .env file in the project folder.

Linux
nano .env

Windows (PowerShell)
notepad .env


Paste and edit (IMPORTANT: replace values):

# Public hostname (your Cloudflare subdomain)
N8N_HOST=n8n.yourdomain.com
N8N_PORT=5678
N8N_PROTOCOL=https

# Webhooks must match the public URL
WEBHOOK_URL=https://n8n.yourdomain.com/

# Use your timezone
GENERIC_TIMEZONE=Asia/Aden

# Strong encryption key (generate one below)
N8N_ENCRYPTION_KEY=CHANGE_ME_TO_A_LONG_RANDOM_KEY

# Basic Auth (recommended)
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=CHANGE_ME_STRONG_PASSWORD

# Optional: reduce diagnostics
N8N_DIAGNOSTICS_ENABLED=false


Generate a strong encryption key:

Linux
openssl rand -hex 32

Windows (PowerShell)
$bytes = New-Object byte[] 32
(New-Object System.Security.Cryptography.RNGCryptoServiceProvider).GetBytes($bytes)
($bytes | ForEach-Object { $_.ToString("x2") }) -join ""

##Step 5 — Create docker-compose.yml

Create a file named docker-compose.yml in the same folder:

services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    env_file:
      - .env
    volumes:
      - n8n_data:/home/node/.n8n
    ports:
      - "127.0.0.1:5678:5678"  # local only (recommended)

  cloudflared:
    image: cloudflare/cloudflared:latest
    container_name: cloudflared
    restart: unless-stopped
    command: tunnel --no-autoupdate run --token ${CLOUDFLARE_TUNNEL_TOKEN}
    environment:
      - CLOUDFLARE_TUNNEL_TOKEN=PASTE_YOUR_TUNNEL_TOKEN_HERE

volumes:
  n8n_data:


✅ Notes:

n8n is bound to localhost only (127.0.0.1) for security.

Cloudflare Tunnel is the only public access.

##Step 6 — Create a Cloudflare Tunnel (Dashboard)

Go to Cloudflare Dashboard → Zero Trust

Go to Networks → Tunnels

Click Create a tunnel

Choose Docker connector

Copy the Tunnel Token

Paste it into docker-compose.yml in:

CLOUDFLARE_TUNNEL_TOKEN=...

##Step 7 — Add a Public Hostname (Cloudflare)

Inside your tunnel settings:

Go to Public Hostname

Add hostname:

Subdomain: n8n

Domain: yourdomain.com

Type: HTTP

URL: http://n8n:5678

Save.

##Step 8 — Start Everything (Run)
Linux
docker compose up -d
docker logs -f n8n

Windows
docker compose up -d
docker logs -f n8n


Test locally (optional):

curl -I http://127.0.0.1:5678


Now open:

https://n8n.yourdomain.com

##Step 9 — Security Checklist (Recommended)

✅ Keep .env secret (do NOT upload it to GitHub)

✅ Use strong password for Basic Auth

✅ Do NOT open port 5678 to the internet

✅ Backup n8n_data regularly

Add this to .gitignore:

.env
*.pem
*.key
secrets/

##Step 10 — Update / Patch (Upgrade n8n Safely)

When you want to update to the latest n8n version:

docker compose pull
docker compose up -d
docker image prune -f


Restart only:

docker compose restart n8n

##Troubleshooting
Tunnel is running but website doesn’t open

Check logs:

docker logs -f cloudflared


Make sure Public Hostname points to:

http://n8n:5678

Webhooks don’t work

Ensure in .env:

N8N_HOST=n8n.yourdomain.com

WEBHOOK_URL=https://n8n.yourdomain.com/

Then restart:

docker compose restart n8n
