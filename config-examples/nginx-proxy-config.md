# Reverse Proxy Configuration for Vaultwarden (Nginx Example)

This document provides guidance on configuring a reverse proxy, specifically Nginx, for your self-hosted Vaultwarden instance. Using a reverse proxy is **highly recommended** to enable HTTPS, which is crucial for security.

**Key Goals:**

* Terminate SSL/TLS (HTTPS).
* Forward requests to the Vaultwarden Docker container.
* Optionally handle WebSocket connections for real-time sync.

## Using Nginx Proxy Manager (Recommended for Simplicity)

If you're using [Nginx Proxy Manager](https://nginxproxymanager.com/), the setup is straightforward through its web UI:

1.  **Add Proxy Host:**
    * **Domain Names:** `vault.yourdomain.com` (or your chosen subdomain).
    * **Scheme:** `http`
    * **Forward Hostname / IP:** The IP address of your Docker host (or `127.0.0.1` if NPM is on the same host) or the Vaultwarden container name if they are on the same Docker network.
    * **Forward Port:** The host port you mapped in `docker-compose.yml` (e.g., `8080` if you used `127.0.0.1:8080:80`).
    * **Enable `Block Common Exploits`**.
    * **Enable `Websockets Support`** (important for real-time sync).
2.  **SSL Tab:**
    * Select an SSL Certificate (e.g., "Request a new SSL Certificate" with Let's Encrypt).
    * Enable `Force SSL` and `HTTP/2 Support`.
    * Enable `HSTS Enabled` for better security.

Save the configuration. NPM will handle certificate renewal.

## Manual Nginx Configuration Example

If you are configuring Nginx manually, here's a basic server block example. You'll need to have Nginx installed and Certbot (or another ACME client) for Let's Encrypt certificates.

**Assumptions:**

* Your Vaultwarden container is accessible on `127.0.0.1:8080` from the Nginx host.
* Your domain is `vault.yourdomain.com`.
* You have SSL certificates (e.g., from Let's Encrypt) located at `/etc/letsencrypt/live/vault.yourdomain.com/`.

Create a new Nginx configuration file (e.g., `/etc/nginx/sites-available/vaultwarden.conf`):

```nginx
server {
    listen 80;
    server_name vault.yourdomain.com;

    # Redirect HTTP to HTTPS
    location / {
        return 301 https://$host$request_uri;
    }

    # Certbot ACME challenge
    location /.well-known/acme-challenge/ {
        root /var/www/html; # Or your Certbot webroot
        allow all;
    }
}

server {
    listen 443 ssl http2;
    server_name vault.yourdomain.com;

    # SSL Configuration
    ssl_certificate /etc/letsencrypt/live/[vault.yourdomain.com/fullchain.pem](https://vault.yourdomain.com/fullchain.pem);
    ssl_certificate_key /etc/letsencrypt/live/[vault.yourdomain.com/privkey.pem](https://vault.yourdomain.com/privkey.pem);
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers off;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:10m; # approx 40,000 sessions
    ssl_session_tickets off;
    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 8.8.8.8 8.8.4.4 valid=300s; # Google Public DNS, or your preferred resolver
    resolver_timeout 5s;

    # Security Headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; font-src 'self'; connect-src 'self' wss://$host;" always;
    # Adjust CSP as needed, 'unsafe-inline' might be required for some Vaultwarden themes/features if not using strict CSP mode.
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;


    # Proxy to Vaultwarden
    location / {
        proxy_pass [http://127.0.0.1:8080](http://127.0.0.1:8080); # Vaultwarden Docker host port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Timeout settings
        proxy_connect_timeout   600;
        proxy_send_timeout      600;
        proxy_read_timeout      600;
        send_timeout            600;
    }

    # WebSocket endpoint for notifications (if WEBSOCKET_ENABLED=true in Vaultwarden .env)
    location /notifications/hub {
        proxy_pass [http://127.0.0.1:8080](http://127.0.0.1:8080); # Vaultwarden Docker host port
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Optional: Access to /admin page
    # You might want to restrict access to this path, e.g., by IP or basic auth
    location /admin {
        proxy_pass [http://127.0.0.1:8080](http://127.0.0.1:8080); # Vaultwarden Docker host port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Example: Restrict by IP
        # allow 192.168.1.10; # Your trusted IP
        # deny all;

        # Example: Basic Auth (create .htpasswd file first)
        # auth_basic "Admin Area";
        # auth_basic_user_file /etc/nginx/.htpasswd_vaultwarden_admin;
    }
}




After creating the file:

Enable the site: sudo ln -s /etc/nginx/sites-available/vaultwarden.conf /etc/nginx/sites-enabled/
Test Nginx configuration: sudo nginx -t
Reload Nginx: sudo systemctl reload nginx
Obtain SSL certificates using Certbot: sudo certbot --nginx -d vault.yourdomain.com (follow prompts). Certbot should automatically update your Nginx config for SSL.
Other Reverse Proxies (Traefik, Caddy)
Traefik: Integrates well with Docker and can automatically discover containers and manage SSL with Let's Encrypt. You would typically add labels to your docker-compose.yml for Traefik.
Caddy: Known for its automatic HTTPS and simple configuration.
Consult the respective documentation for Traefik or Caddy for detailed setup instructions.

Always ensure your DOMAIN variable in the Vaultwarden .env file matches the HTTPS URL you configure.