FROM ubuntu:latest

WORKDIR /src

RUN apt update && apt install -y \
    libpcre3 \
    libpcre3-dev \
    wget \
    git \
    gcc \
    make \
    libbrotli-dev \
    zlib1g-dev

RUN wget https://nginx.org/download/nginx-1.27.4.tar.gz && tar -zxf nginx-1.27.4.tar.gz && \
    wget https://ftp.openbsd.org/pub/OpenBSD/LibreSSL/libressl-4.0.0.tar.gz && tar -zxf libressl-4.0.0.tar.gz && \
    git clone --recurse-submodules -j8 https://github.com/google/ngx_brotli.git

# Configure and build Nginx with LibreSSL
RUN cd nginx-1.27.4 && ./configure \
    --add-dynamic-module=../ngx_brotli \
    --prefix=/usr/share/nginx \
    --conf-path=/etc/nginx/nginx.conf \
    --http-log-path=/var/log/nginx/access.log \
    --error-log-path=/var/log/nginx/error.log \
    --lock-path=/var/lock/nginx.lock \
    --pid-path=/run/nginx.pid \
    --modules-path=/usr/lib/nginx/modules \
    --http-client-body-temp-path=/var/lib/nginx/body \
    --http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
    --http-proxy-temp-path=/var/lib/nginx/proxy \
    --http-scgi-temp-path=/var/lib/nginx/scgi \
    --http-uwsgi-temp-path=/var/lib/nginx/uwsgi \
    --with-debug \
    --with-pcre-jit \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-http_realip_module \
    --with-http_auth_request_module \
    --with-http_v2_module \
    --with-http_v3_module \
    --with-http_dav_module \
    --with-http_slice_module \
    --with-threads \
    --with-file-aio \
    --with-http_addition_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_sub_module \
    --with-openssl=../libressl-4.0.0 && \
    make -j8 && make install

COPY ./dhparam2048.pem /etc/nginx

RUN mkdir -p /var/lib/nginx/{body,fastcgi}

ENV PATH="$PATH:/usr/share/nginx/sbin"
