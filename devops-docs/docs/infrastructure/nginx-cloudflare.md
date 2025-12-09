# Secure Nginx Reverse Proxy Setup with Cloudflare Proxy

This guide covers setting up Nginx as a reverse proxy with Cloudflare handling SSL/TLS termination for secure, scalable deployments.

## Prerequisites

Before proceeding, ensure:

- You have **root or sudo privileges**
- **Nginx is installed and running**
- Your domain is properly pointed to **Cloudflare and proxied** (orange cloud enabled) in Cloudflare's DNS settings

## Step 1: Configure Nginx Reverse Proxy

If you are running a backend service on port 8000, configure Nginx as a reverse proxy.

### Create a New Nginx Configuration File

Replace `crm.hostweblankan.in` with your actual domain:

```bash
sudo nano /etc/nginx/sites-available/crm.hostweblankan.in
```

Paste the following configuration:

```nginx
server {
    listen 80;
    server_name crm.hostweblankan.in;

    location / {
        proxy_pass http://127.0.0.1:8000;  # Ensure the upstream matches your app setup
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Connection "";
    }
}
```

### Save and Exit

Press the following keys in sequence:

1. Press **CTRL + X** - Exit editor
2. Press **Y** - Confirm save
3. Press **ENTER** - Confirm filename

## Step 2: Enable Configuration and Restart Nginx

Enable the Nginx configuration and verify it:

```bash
# Create symbolic link to enable the site
sudo ln -s /etc/nginx/sites-available/crm.hostweblankan.in /etc/nginx/sites-enabled/

# Test Nginx configuration for syntax errors
sudo nginx -t

# Restart Nginx to apply changes
sudo systemctl restart nginx
```

## Step 3: Cloudflare SSL Settings

Since Cloudflare is proxying your domain, it handles SSL/TLS termination. You do not need to install SSL certificates on your server.

### Configure Cloudflare Dashboard

1. Navigate to **Cloudflare Dashboard** → **SSL/TLS Settings**
2. Set **SSL mode** to:
   - **"Full"** (Recommended) - If your backend supports HTTPS
   - **"Flexible"** - If your backend only supports HTTP
3. **Disable** "Always Use HTTPS" (Cloudflare handles it automatically)
4. **Enable** "Automatic HTTPS Rewrites" for better compatibility

!!! tip "Best Practice"
    Use **Full Mode** for production environments to ensure end-to-end encryption.

## Step 4: Verify Your Setup

Once the setup is complete, visit your domain:

```
https://crm.hostweblankan.in
```

Since Cloudflare handles SSL/TLS, your Nginx only listens on port 80, and Cloudflare manages the secure HTTPS connection to clients.

## How It Works

```
Client (HTTPS) 
    ↓
Cloudflare (SSL Termination)
    ↓
Your Server (HTTP port 80)
    ↓
Nginx (Reverse Proxy)
    ↓
Backend Application (Port 8000)
```

## Troubleshooting

### Check Nginx Status

View the current status of Nginx:

```bash
sudo systemctl status nginx
```

**Expected Output:**
```
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since ...
```

### Check Logs for Errors

View system and Nginx error logs:

```bash
# View system journal (last 50 lines)
sudo journalctl -xe

# Real-time Nginx error log
sudo tail -f /var/log/nginx/error.log
```

### Check if Nginx is Listening on Port 80

Verify Nginx is bound to port 80:

```bash
sudo netstat -tulnp | grep nginx
```

**Expected Output:**
```
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1234/nginx: master
tcp6       0      0 :::80                   :::*                    LISTEN      1234/nginx: master
```

### Common Issues

| Issue | Solution |
|-------|----------|
| **Port 80 already in use** | `sudo lsof -i :80` to find process; stop it or change Nginx port |
| **DNS not resolving** | Verify orange cloud is enabled in Cloudflare DNS settings |
| **502 Bad Gateway** | Check backend service on port 8000 is running; verify proxy_pass URL |
| **SSL certificate errors** | Ensure Cloudflare SSL mode is set to "Full" or "Flexible" |
| **Permission denied errors** | Ensure commands run with `sudo`; verify file permissions |

## Security Best Practices

1. **Firewall Rules**: Only allow Cloudflare IPs to connect to your server
   ```bash
   sudo ufw allow from 173.245.48.0/20 to any port 80
   sudo ufw allow from 103.21.244.0/22 to any port 80
   # (Add all Cloudflare IP ranges)
   ```

2. **Hide Backend IP**: Cloudflare proxying naturally hides your origin server IP

3. **Rate Limiting**: Enable Cloudflare's built-in rate limiting in dashboard

4. **Monitor Logs**: Regularly check `/var/log/nginx/access.log` for suspicious patterns

5. **Keep Nginx Updated**: Regularly update Nginx to latest stable version
   ```bash
   sudo apt update && sudo apt upgrade nginx
   ```

## Additional Resources

- [Cloudflare SSL/TLS Documentation](https://developers.cloudflare.com/ssl/)
- [Nginx Reverse Proxy Documentation](https://nginx.org/en/docs/http/ngx_http_proxy_module.html)
- [Cloudflare IP Ranges](https://www.cloudflare.com/ips/)
