Self-Hosted n8n on Oracle Cloud
 A production-ready self-hosted deployment of n8n workflow automation platform on Oracle Cloud Free Tier, using Docker, Nginx as reverse proxy, and Let's Encrypt SSL certificate.

Live Demo
 petprojectcloudlytvynenkonikita.duckdns.org

Tech Stack
 Oracle Cloud Infrastructure - Ubuntu 22.04
 Docker + Docker Compose - containerization and orchestration
 Nginx - reverse proxy with SSL termination
 Let's Encrypt/Certbot - free SSL certificate via DNS challenge
 DuckDNS - free dynamic DNS
 Bash - automated backup script with cron

Architecture
 Internet:
  Oracle Cloude (Security list + NSG)
  Ubuntu 22.04 VM
  iptables
  Nginx (port 443, SSl termination)
  n8n container

Project Structure
n8n-self-hosted/
  docker-compose.yaml
  nginx/
   n8n.conf
  scripts
   bacckup.sh
  .env.example
  README.md

Prerequisites
Oracle Cloude
Domain/DuckDNS subdomain
Ubuntu 22.04 VM + Docker installed

Setup
1. Clone repository
 git clone https://github.com/YOUR_USERNAME/n8n-self-hosted.git
 cd n8n-self-hosted

2. Configure environment
 cp .env.example .env
 nano .env

3. Obtain SSL certificate
 sudo certbot certonly \
  --authenticator dns-duckdns \
  --dns-duckdns-credentials ~/duckdns.ini \ 
 --dns-duckdns-propagation-seconds 60 \
  -d your-domain.duckdns.org

4. Start services
 docker compose up -d

5. Configure Oracle Cloud firewall
 In Security List add Ingress Rules
 port: 22, 80, 443
 protocol: TCP for all
 Source: 0.0.0.0/0 for all

!leave Source Port Range empty!

6. Open ports in Ubuntu iptables
 sudo iptables -I INPUT -p tcp --dport 80 -j ACCEPT
 sudo iptables -I INPUT -p tcp --dport 443 -j ACCEPT
 sudo iptables -I FORWARD -j ACCEPT
 sudo netfilter-persistent save

7. Automated Backups
 chmod +x scripts/backup.sh
 crontab -e

Security Notes
 .env file is excluded from git via .gitignore
 SSL certificate auto-renews via Certbot
 n8n is not exposed directly - all traffic goes through Nginx
 DuckDNS credentials stored with chmod 600 permissions

Key Learnings
 Oracle Cloud has two separate firewall layers (Security List + iptables)  both must be configured
 Source Port Range in Security List must be empty, not equal to destination port
 Docker requires FORWARD chain rules in addition to INPUT
 DNS challenge for SSL is useful when port 80 is blocked
