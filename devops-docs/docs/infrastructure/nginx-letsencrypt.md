# Secure Nginx Reverse Proxy with Let's Encrypt SSL

This guide configures Nginx as a reverse proxy with free Let's Encrypt SSL using Certbot.

## Prerequisites

- Root or sudo privileges
- Nginx installed and running
- Domain pointed to this server via DNS A/AAAA records
- Ports 80/443 reachable from the internet

## Step 1: Install Certbot and Nginx plugin

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```

## Step 2: Configure Nginx reverse proxy (HTTP)

Create the site config (replace `crm.hostweblankan.in` with your domain):

```bash
sudo nano /etc/nginx/sites-available/crm.hostweblankan.in
```

Paste:

```nginx
server {
    listen 80;
    server_name crm.hostweblankan.in;

    location / {
        proxy_pass http://127.0.0.1:8000;  # Upstream app
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Connection "";
    }
}
```

Save and exit (CTRL+X, Y, ENTER).

## Step 3: Enable and restart Nginx

```bash
sudo ln -s /etc/nginx/sites-available/crm.hostweblankan.in /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## Step 4: Obtain SSL certificate (auto-configure Nginx)

```bash
sudo certbot --nginx -d crm.hostweblankan.in -d www.crm.hostweblankan.in
```

- `--nginx`: Edits Nginx to add HTTPS server block
- `-d`: Request certs for root and www

## Step 5: Enable auto-renewal

Test renewal:

```bash
sudo certbot renew --dry-run
```

Add cron for daily renewal at 03:00:

```bash
sudo crontab -e
# add:
0 3 * * * certbot renew --quiet && systemctl reload nginx
```

## Step 6: Verify

- Visit: `https://crm.hostweblankan.in`
- Check certs:

```bash
sudo certbot certificates
```

## Troubleshooting

- Nginx status: `sudo systemctl status nginx`
- Logs: `sudo journalctl -xe` and `sudo tail -f /var/log/nginx/error.log`
- Force renew: `sudo certbot renew --force-renewal`

## Optimized HTTPS config for WordPress (Docker upstream example)

```nginx
# HTTPS Server Block
server {
    listen 443 ssl;
    server_name <yourdomain.com> www.<yourdomain.com>;

    ssl_certificate /etc/letsencrypt/live/<yourdomain.com>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<yourdomain.com>/privkey.pem;

    client_max_body_size 256M;

    # Gzip Compression
    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Browser Caching
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|pdf|svg|woff|woff2|ttf|eot)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform";
    }

    # FastCGI Cache Configuration (toggle per request)
    set $no_cache 0;
    if ($request_method = POST) { set $no_cache 1; }
    if ($query_string != "") { set $no_cache 1; }

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Connection "";

        # FastCGI-like cache for proxied app
        proxy_cache my_cache;
        proxy_cache_valid 200 301 302 10m;
        proxy_cache_valid 404 1m;
        proxy_cache_bypass $no_cache;
        proxy_no_cache $no_cache;
        proxy_cache_use_stale error timeout updating;
        add_header X-Cache-Status $upstream_cache_status;
    }
}

server {
    listen 80;
    server_name <yourdomain.com> www.<yourdomain.com>;

    # Redirect HTTP to HTTPS
    if ($host = "<yourdomain.com>") {
        return 301 https://www.<yourdomain.com>$request_uri;
    }

    return 301 https://$host$request_uri;
}

# FastCGI Cache Path Configuration
proxy_cache_path /var/cache/nginx/my_cache levels=1:2 keys_zone=my_cache:10m max_size=500m inactive=60m use_temp_path=off;
```

## Notes

- Replace `<yourdomain.com>` everywhere with your domain.
- Ensure upstream ports match your app (examples: 8000 for app, 8080 for WordPress container).
- Certbot will handle future renewals automatically once cron is set.
