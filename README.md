# Nginx Reverse Proxy

Configured Nginx as a reverse proxy on an AWS EC2 instance (Amazon Linux), forwarding public HTTP traffic to a backend app running on port 3000.

## Architecture

```
Browser → port 80 → Nginx → port 3000 → Backend App
```

The backend app never exposes itself directly to the internet. Nginx handles all public-facing traffic.

## Setup

**Environment:** AWS EC2 (Amazon Linux, t2.micro)

**1. Install and start Nginx**
```bash
sudo dnf install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

**2. Run a backend app on port 3000**
```bash
mkdir ~/myapp && cd ~/myapp
echo "<h1>Hello from Lari's backend app</h1>" > index.html
python3 -m http.server 3000 &
```

**3. Configure Nginx to proxy port 80 → port 3000**

Edit `/etc/nginx/nginx.conf` and replace the `location /` block:

```nginx
location / {
    proxy_pass http://localhost:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## Result

Visiting `http://<ec2-ip>` in a browser returns the response from the backend app on port 3000 — routed through Nginx on port 80.

## Screenshots

| Browser | Terminal |
|---|---|
| ![Browser](screenshots/browser-proxy-working.png) | ![Terminal](screenshots/curl-proxy-working.png) |
