FROM debian:buster
MAINTAINER Anders Åslund <anders.aslund@teknoir.se>

VOLUME /etc/letsencrypt
EXPOSE 80
EXPOSE 443

WORKDIR /opt
RUN apt-get update && apt-get -y upgrade && \
    apt-get install -y gettext-base wget libpcre3-dev build-essential libssl-dev zlib1g-dev python3 python3-dev libffi6 libffi-dev libssl-dev curl build-essential procps && \
    wget http://nginx.org/download/nginx-1.18.0.tar.gz && \
    tar -zxvf nginx-1.*.tar.gz && \
    cd nginx-1.* && \
    ./configure --prefix=/opt/nginx --user=nginx --group=nginx --with-http_ssl_module --with-ipv6 --with-threads --with-stream --with-stream_ssl_module --with-http_v2_module && \
    make && make install && \
    cd .. && rm -rf nginx-1.* && \
    adduser --system --no-create-home --disabled-login --disabled-password --group nginx && \
    mkdir /opt/nginx/http.conf.d && mkdir /opt/nginx/stream.conf.d && \
    curl -L 'https://bootstrap.pypa.io/get-pip.py' | python3 && \
    pip install -U cffi certbot && \
    wget https://raw.githubusercontent.com/certbot/certbot/master/certbot/certbot/ssl-dhparams.pem -O /opt/nginx/ssl-dhparams.pem && \
    wget https://raw.githubusercontent.com/certbot/certbot/master/certbot-nginx/certbot_nginx/_internal/tls_configs/options-ssl-nginx.conf -O /opt/nginx/options-ssl-nginx.conf && \
    apt remove --purge -y wget libpcre3-dev build-essential libssl-dev zlib1g-dev python3-dev build-essential libffi-dev libssl-dev curl && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy in scripts for certbot
COPY nginx.conf /opt/nginx/conf/nginx.conf
COPY nginx_conf.d/ /opt/nginx/conf.d/
COPY scripts/zero_downtime_reload.sh /opt/nginx/sbin/zero_downtime_reload.sh
COPY ./scripts/ /scripts
RUN chmod +x /scripts/*.sh

# Add /scripts/startup directory to source more startup scripts
RUN mkdir -p /scripts/startup

# Copy in default nginx configuration (which just forwards ACME requests to
# certbot, or redirects to HTTPS, but has no HTTPS configurations by default).

ENTRYPOINT []
CMD ["/bin/bash", "/scripts/entrypoint.sh"]
