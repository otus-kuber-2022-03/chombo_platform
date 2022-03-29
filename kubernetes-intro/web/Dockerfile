FROM nginx:1.20.2-alpine

ENV GID=1001 \
    UID=1001 \
    USER=nginx \
    GROUP=nginx

RUN set -x; \
    deluser ${USER}; \
    addgroup -g ${GID} -S ${GROUP}; \
    adduser -S -D -H -u ${UID} -h /var/cache/nginx -s /sbin/nologin -G ${GROUP} -g ${GROUP} ${USER}; \
    chown -R nginx:nginx /var/cache/nginx; \
    chown -R nginx:nginx /var/log/nginx; \
    chown -R nginx:nginx /etc/nginx/conf.d; \
    touch /var/run/nginx.pid; \
    chown -R nginx:nginx /var/run/nginx.pid; \
    rm /etc/nginx/conf.d/default.conf; \
    mkdir /app; \
    chown -R nginx:nginx /app; \
    { \
    echo 'server {'; \
    echo '    listen 8000;'; \
    echo '    server_name localhost;'; \
    echo '    root /usr/share/nginx/html;'; \
    echo '    index index.html index.htm;'; \
    echo; \
    echo '    location /healthcheck {'; \
    echo '        return 200;'; \
    echo '        access_log off;'; \
    echo '        allow 127.0.0.1/32;'; \
    echo '        deny all;'; \
    echo '    }'; \
    echo; \
    echo '    location /status {'; \
    echo '        stub_status on;'; \
    echo '        access_log off;'; \
    echo '        allow 127.0.0.1/32;'; \
    echo '        deny all;'; \
    echo '    }'; \
    echo; \
    echo '    location / {'; \
    echo '        alias   /app/;'; \
    echo '    }'; \
    echo; \
    echo '    location ~ /\. {'; \
    echo '        deny all;'; \
    echo '        access_log off;'; \
    echo '        log_not_found off;'; \
    echo '    }'; \
    echo; \
    echo '    error_page   500 502 503 504  /50x.html;'; \
    echo '    location = /50x.html {'; \
    echo '        root   /usr/share/nginx/html;'; \
    echo '    }'; \
    echo '}'; \
	  } | tee /etc/nginx/conf.d/01-host.conf

USER ${USER}
WORKDIR /app
