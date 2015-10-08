# tutum-docker-registry

Tutum Stackfile for Docker registry with web UI

[![Deploy to Tutum](https://s.tutum.co/deploy-to-tutum.svg)](https://dashboard.tutum.co/stack/deploy/)

## Docker registry v1 with web UI

- registry:latest
- konradkleine/docker-registry-frontend:latest

## Nginx vhosts configurations example

### For docker registry

    server {
        server_name registry.myregistry.com;
        listen 80;
        rewrite ^ https://$server_name$request_uri? permanent;
    }
    server {
        server_name registry.myregistry.com;
        listen 443;
        ssl on;
        proxy_ssl_session_reuse off;
        ssl_certificate /etc/nginx/ssl/myregistry-chained.crt;
        ssl_certificate_key /etc/nginx/ssl/myregistry.key;
        access_log /var/log/nginx/registry.myregistry.com-access.log;
        error_log /var/log/nginx/registry.myregistry.com-error.log;
        client_max_body_size 0;
        chunked_transfer_encoding on;
        auth_basic Restricted;
        auth_basic_user_file /etc/nginx/htpasswd/myregistry;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Authorization "" ;
        proxy_read_timeout 900;
        location / {
            proxy_pass http://myserver:5000;
        }
        location ~ (/v1)?/_ping {
            proxy_pass http://myserver:5000;
        }
    }

### For registry web UI

    server {
        server_name registry-ui.myregistry.com;
        listen 80;
        rewrite ^ https://$server_name$request_uri? permanent;
    }
    server {
        server_name registry-ui.myregistry.com;
        listen 443;
        ssl on;
        proxy_ssl_session_reuse off;
        ssl_certificate /etc/nginx/ssl/myregistry-chained.crt;
        ssl_certificate_key /etc/nginx/ssl/myregistry.key;
        access_log /var/log/nginx/registry-ui.myregistry.com-access.log;
        error_log /var/log/nginx/registry-ui.myregistry.com-error.log;
        client_max_body_size 0;
        chunked_transfer_encoding on;
        auth_basic Restricted;
        auth_basic_user_file /etc/nginx/htpasswd/myregistry;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Authorization "" ;
        proxy_read_timeout 900;
        location / {
            proxy_pass http://myserver:5001;
        }
    }