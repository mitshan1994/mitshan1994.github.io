---
layout: post
title:  "Nginx syntax"
categories: nginx
---

### Location
**location** can be used to process different http/https request uri mode with different methods.

Here I tested a simple usage in the below:
```
http {
    server {
        listen 8000;
        
        location /api/ {
            proxy_pass http://127.0.0.1:5125;
        }
        
        # All others
        location / {
            proxy_pass http://127.0.0.1:8088;
        }
    }
    
    # HTTPS
    server {
        listen 8443 ssl;
        ssl_certificate /path/to/aaa.crt;
        ssl_certificate_key /path/to/aaa.key;

        # Pass https to http backend.
        location / {
            proxy_pass http://127.0.0.1:8088;
        }
    }
}
```
Requests of **/api/\*** will be redirected to port 5125, and all others will be redirected to port 8088. When I test, the match is case insensitive.

The config above also add a https reverse proxy, which deload the encrypt/decrypt from backends.
