# Simple nginx Config file

```
pid /run/nginx.pid;
events {
}

http {
        include mime.types;
        # show default index.html file from /var/www/html location when http request for all urls
        server {
                listen 80;
                server_name xxxxxx.xxx;

                root /var/www/html;
        }
        # redirect any http request made to sub domain to https 
        server {
                listen 80;
                server_name api.xxxxxx.xxx;

                return 301 https://$host$request_uri;
        }

        # show default index.html file from /var/www/html location when https request were made, and when requested specific uri, then reverse proxy into another server
        server {
                listen 443 ssl http2;
                server_name api.antany.ca;

                ssl_certificate /etc/nginx/keys/certificate.pem;
                ssl_certificate_key /etc/nginx/keys/certificate.pem.key;

                location /xxxxx{
                        proxy_pass https://localhost:8443/xxxxx;
                }

                location / {
                        root /var/www/ssl;
                }
        }
}
```
