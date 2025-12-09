# Optimized .htaccess Configuration

Replace `yourdomain.com` with your actual domain before using.

## Full .htaccess (copy-paste ready)

```apacheconf
# WordPress Rules
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteRule ^index\.php$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.php [L]
</IfModule>

# Enable Gzip Compression (Improves Speed)
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain text/html text/xml text/css application/javascript application/json
</IfModule>

# Enable Browser Caching (Improves Performance)
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType text/css "access plus 1 month"
    ExpiresByType text/x-javascript "access plus 1 month"
    ExpiresByType application/javascript "access plus 1 month"
    ExpiresByType application/pdf "access plus 1 month"
    ExpiresByType application/x-shockwave-flash "access plus 1 month"
    ExpiresByType image/x-icon "access plus 1 year"
    ExpiresDefault "access plus 2 days"
</IfModule>

# Block Hotlinking (Prevents Image Theft)
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{HTTP_REFERER} !^$
    RewriteCond %{HTTP_REFERER} !^http(s)?://(www\.)?yourdomain.com [NC]
    RewriteRule \.(jpg|jpeg|png|gif)$ - [NC,F,L]
</IfModule>

# Enable Keep-Alive (Reduces Server Overhead)
<IfModule mod_headers.c>
    Header set Connection keep-alive
</IfModule>

# Disable Directory Browsing (Security)
Options -Indexes

# Protect wp-config.php (WordPress Security)
<Files wp-config.php>
    order allow,deny
    deny from all
</Files>
```

## Usage notes

- Place this in your site root `.htaccess` (Apache). Not used by Nginx.
- Adjust caching durations to match your release cadence.
- After edits, test with `apachectl configtest` (or `apache2ctl`) and reload Apache.
- For mixed HTTP/HTTPS sites, ensure rewrites and canonical host rules are handled elsewhere if needed.
