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
    echo '    server_name _;'; \
    echo '    root /app;'; \
    echo '    index index.html index.htm;'; \
    echo '    location / {'; \
    echo '        try_files $uri $uri/ =404;'; \
    echo '    }'; \
    echo '}'; \
	  } | tee /etc/nginx/conf.d/01-host.conf

USER ${USER}
WORKDIR /app
EXPOSE 8000
