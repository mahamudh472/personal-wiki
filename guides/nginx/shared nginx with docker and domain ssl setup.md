

Assume we are running multiple servers in a machine and we need to use nginx for them all. The machine can only have one nginx. But we can make the host nginx to serve the different nginx.

We can create multiple nginx in Docker containers.

Assuming that the docker setup for nginx and the application is ready and the application nginx is availbale on any port but not 80 or 443(Example: 8888).

Assuming that our url is accessable on http://host_ip:port now setup the host nginx to point our docker nginx.

First we need to add our config on nginx sites-available
```bash
sudo nano /etc/nginx/sites-available/site-domain
```

Add the code code inside.

```nginx
server {
    listen 80;
    server_name site-domain;

    location / {
        proxy_pass http://127.0.0.1:<the docker nginx port>;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Add symlink

```bash
sudo ln -s /etc/nginx/sites-available/site-domain /etc/nginx/sites-enabled/site-domain
```

Test and reload nginx
```bash
sudo nginx -t && sudo systemctl reload nginx
```

We need to add the host ip in the domain DNS record. Now, we should be able to access our applicaiton in site-domain.

Now, If we want to add ssl certificate, the steps are sraight forward.

Install CertBot:
```bash 
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```
Generate ssl certificate
```bash
sudo certbot --nginx -d api-remnant.duckdns.org
```
Certbot will automatically:

- Obtain the SSL certificate
- Modify your host nginx config to add HTTPS (443)
- Add HTTP → HTTPS redirect

Need to add the follwing header in docker nginx `location /`
```nginx
proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;  # ← changed
```
