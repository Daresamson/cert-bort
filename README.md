
<img width="1366" height="768" alt="Screenshot (395)" src="https://github.com/user-attachments/assets/384fef09-c485-4384-ba0e-8fd3230b8314" />


<img width="1366" height="768" alt="Screenshot (399)" src="https://github.com/user-attachments/assets/a6069ed3-277b-4e4a-bc2a-a2984640fd59" />

<img width="1366" height="768" alt="Screenshot (399)" src="https://github.com/user-attachments/assets/818ef33d-c287-42f2-8b16-2d3ac6a6a2be" />


# Hosting Multiple Subdomains with NGINX and Certbot on Amazon Linux 2023

## 1. Overview / Objective

This guide provides a step-by-step process for setting up multiple subdomains on Amazon Linux 2023 using NGINX and securing them with HTTPS certificates issued by Certbot.

## 2. Server Information

* **Operating System:** Amazon Linux 2023
* **NGINX Version:** Installed from default `dnf` repository
* **Certbot Version:** Installed using `python3-certbot-nginx`

## 3. Update System and Install NGINX

Update system packages and install NGINX:

```bash
sudo dnf install nginx -y
```

Enable and start NGINX:

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

Check NGINX status:

```bash
sudo systemctl status nginx
```

## 4. Create Directory Structure for Websites

Create directories for each subdomain:

```bash
sudo mkdir -p /var/www/www
sudo mkdir -p /var/www/api
sudo mkdir -p /var/www/grafana
sudo mkdir -p /var/www/prometheus
sudo mkdir -p /var/www/loki
sudo mkdir -p /var/www/sonarqube
```

Add test index pages:

```bash
echo '<h1>Welcome to www.celefutaconnect.org</h1>' | sudo tee /var/www/www/index.html
echo '<h1>Welcome to api.celefutaconnect.org</h1>' | sudo tee /var/www/api/index.html
```

Repeat similarly for the other subdomains (`grafana`, `prometheus`, `loki`, `sonarqube`).

## 5. Configure NGINX Virtual Hosts

Example configuration for `www` subdomain (`/etc/nginx/conf.d/www.conf`):

```nginx
server {
    listen 80;
    server_name www.celefutaconnect.org;
    root /var/www/www;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Repeat similar configuration files for `api`, `grafana`, `prometheus`, `loki`, and `sonarqube` subdomains.

Test NGINX configuration:

```bash
sudo nginx -t
```

Reload NGINX:

```bash
sudo systemctl reload nginx
```

## 6. Install Certbot and Enable HTTPS

Install Certbot with NGINX plugin:

```bash
sudo dnf install certbot python3-certbot-nginx -y
```

Generate SSL certificates for all subdomains:

```bash
sudo certbot --nginx \
-d www.celefutaconnect.org \
-d api.celefutaconnect.org \
-d grafana.celefutaconnect.org \
-d prometheus.celefutaconnect.org \
-d loki.celefutaconnect.org \
-d sonarqube.celefutaconnect.org
```

## 7. Verify SSL Certificates

Check automatic renewal:

```bash
sudo certbot renew --dry-run
```

## 8. Test Websites

Open a browser and verify HTTPS access for each subdomain:

* [https://www.celefutaconnect.org](https://www.celefutaconnect.org)
* [https://api.celefutaconnect.org](https://api.celefutaconnect.org)
* [https://grafana.celefutaconnect.org](https://grafana.celefutaconnect.org)
* [https://prometheus.celefutaconnect.org](https://prometheus.celefutaconnect.org)
* [https://loki.celefutaconnect.org](https://loki.celefutaconnect.org)
* [https://sonarqube.celefutaconnect.org](https://sonarqube.celefutaconnect.org)

## 9. Troubleshooting Notes

1. Ensure ports 80 and 443 are open in the server firewall/security groups.
2. Check NGINX error logs for misconfigurations: `/var/log/nginx/error.log`
3. Certbot auto-renewal uses systemd timers by default on Amazon Linux 2023.
4. Validate DNS records for each subdomain pointing to the server IP.

