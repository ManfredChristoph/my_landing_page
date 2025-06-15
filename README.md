
# Cloud-Deployed Landing Page with Nginx Reverse Proxy

This project demonstrates how to deploy a dynamic landing page using **Vercel**, and serve it through a self-managed **Nginx reverse proxy on an AWS EC2 Ubuntu server**, secured with **Let's Encrypt SSL**.

---

## Live Deployment

- Custom Domain: (https://manfredchristoph.duckdns.org)
- EC2 Public IP Address: `13.40.198.215`
- Vercel App: (https://manfred-christoph.vercel.app)

---

## Stack Used

- **Vercel**  Frontend deployment
- **Nginx**  Web server & reverse proxy
- **AWS EC2 (Ubuntu 24.04 LTS)** Hosting server
- **DuckDNS** Free custom domain
- **Certbot (Let's Encrypt)** Free SSL certificate

---

## Step-by-Step Setup Guide

### 1. Building a Dynamic Landing Page
- I used Html and CSS to build my landing page with CSS animations for enhancements.


### 2. Deploy Landing Page on Vercel
- Pushed my HTML/CSS files to GitHub
- Imported the repo to Vercel (https://vercel.com)
- Deployed the project


### 3. Provision the Server (EC2)
- Launched an EC2 instance (Ubuntu 24.04 LTS)
- Allowed inbound traffic for:
  - Port 22 (SSH)
  - Port 80 (HTTP)
  - Port 443 (HTTPS)


### 4. Domain Configuration (DuckDNS)
- Created a subdomain via (https://www.duckdns.org)
- Pointed the subdomain to my EC2 public IP

### 5. Install Nginx on Ubuntu
- Installed nginx using these codes below;
sudo apt update
sudo apt install nginx -y


### 6. Configure Nginx for Reverse Proxy
- I edited the default Nginx site config using:

sudo nano /etc/nginx/sites-available/default

and Replace its content with:

server {
    listen 80;
    server_name manfredchristoph.duckdns.org;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name manfredchristoph.duckdns.org;

    ssl_certificate /etc/letsencrypt/live/manfredchristoph.duckdns.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/manfredchristoph.duckdns.org/privkey.pem;

    location / {
        proxy_pass https://manfred-christoph.vercel.app;
        proxy_set_header Host manfred-christoph.vercel.app;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

then;

Tested & reloaded nginx using:

sudo nginx -t
sudo systemctl reload nginx


### 7. Install Certbot for SSL
- I used the code below;

sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d manfredchristoph.duckdns.org

and followed prompts and confirm the domain is secure via HTTPS.


### 8. Reverse Proxy to Vercel
- Already done in Nginx config (`proxy_pass` to the Vercel URL)


### 9. Screenshot of Rendered page in a browser.
![Image](/assets/landingpage.png)

