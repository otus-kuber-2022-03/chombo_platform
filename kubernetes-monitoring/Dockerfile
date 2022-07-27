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
    echo '    listen 80;'; \
    echo '    server_name _;'; \
    echo '    root /app;'; \
    echo '    index index.html index.htm;'; \
    echo '    location = /basic_status {'; \
    echo '        stub_status;'; \
    echo '    }'; \
    echo '    location / {'; \
    echo '        return 200 "OK";'; \
    echo '        add_header Content-Type text/plain;'; \
    echo '    }'; \
    echo '}'; \
	  } | tee /etc/nginx/conf.d/01-host.conf

USER ${USER}
WORKDIR /app
EXPOSE 80
