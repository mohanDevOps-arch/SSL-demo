# Nginx Static Website Setup with SSL on Ubuntu

This document provides a step-by-step guide to host a static website on **Nginx**, deploy HTML files, and secure it using **Let's Encrypt SSL** with **Certbot**.

---

## Prerequisites
- Ubuntu Server (e.g., AWS EC2 instance)
- Domain name (e.g., `vedase.site`)
- DNS A records:
  | Type | Name | Value | TTL |
  |------|------|--------|-----|
  | A | @ | `<EC2 Public IP>` | 3600 |
  | A | www | `<EC2 Public IP>` | 3600 |

---

## Step 1: Install and Configure Nginx

```bash
sudo apt update
sudo apt install nginx -y
```

Verify installation:
```bash
sudo systemctl status nginx
```

---

## Step 2: Deploy Website Files

Clone your project repository:
```bash
cd ~
sudo git clone https://github.com/mohanDevOps-arch/SSL-demo.git
```

Copy the HTML files to Nginx’s default web directory:
```bash
sudo cp SSL-demo/admin.html /var/www/html
sudo cp SSL-demo/index.html /var/www/html
sudo cp SSL-demo/dashboard.html /var/www/html
```

Remove the default Nginx landing page:
```bash
sudo rm -rf /var/www/html/index.nginx-debian.html
```

---

## Step 3: Configure Nginx Server Block

Navigate to the Nginx config directory:
```bash
cd /etc/nginx/sites-available/
```

Open and edit the default configuration file:
```bash
sudo nano default
```

Add the following configuration:

```nginx
server {
    listen 80;
    server_name vedase.site www.vedase.site;

    root /var/www/html;
    index index.html;

    # Homepage route
    location = / {
        try_files /index.html =404;
    }

    # Route for /admin -> admin.html
    location = /admin {
        try_files /admin.html =404;
    }

    # Route for /dashboard -> dashboard.html
    location = /dashboard {
        try_files /dashboard.html =404;
    }

    # Handle static assets
    location / {
        try_files $uri $uri/ =404;
    }
}
```

Test configuration:
```bash
sudo nginx -t
```

Reload Nginx:
```bash
sudo systemctl reload nginx
```

---

## Step 4: Install Certbot and Enable SSL

Install Certbot:
```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```

Run Certbot to automatically generate and configure SSL:
```bash
sudo certbot --nginx -d vedase.site -d www.vedase.site
```

Certbot will:
- Verify domain ownership
- Generate SSL certificates
- Update your Nginx configuration to use HTTPS

---

## Step 5: Verify Setup

Check DNS records:
```bash
dig +short vedase.site
dig +short www.vedase.site
```

Reload and test:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

Access your website:
- 🌐 [https://vedase.site](https://vedase.site)
- 🌐 [https://www.vedase.site](https://www.vedase.site)

---

## Step 6: Auto-Renew SSL

Certbot auto-renewal is configured via `systemd` or cron.
You can manually test renewal with:
```bash
sudo certbot renew --dry-run
```

---

## Summary

| Task | Command |
|------|----------|
| Install Nginx | `sudo apt install nginx -y` |
| Deploy HTML files | `sudo cp *.html /var/www/html` |
| Configure Nginx | `/etc/nginx/sites-available/default` |
| Install SSL | `sudo certbot --nginx -d vedase.site -d www.vedase.site` |
| Reload Nginx | `sudo systemctl reload nginx` |

---

**Author:** Mohan Krishna  
**Project Repository:** [mohanDevOps-arch/SSL-demo](https://github.com/mohanDevOps-arch/SSL-demo)
