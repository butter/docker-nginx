FROM debian:jessie
MAINTAINER Adam Walz <adam@butter.ai>

EXPOSE 80 443

ENV \
  NGINX_DEVEL_KIT_VERSION=0.3.0 \
  LUA_NGINX_MODULE_VERSION=0.10.6 \
  NGINX_VERSION=1.11.3 \
  LUAJIT_VERSION=2.0.4

RUN groupadd -r nginx \
 && useradd -r -g nginx nginx

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    wget \
    make \
    libpcre3 \
    libpcre3-dev \
    zlib1g-dev \
    libssl-dev \
    gcc \
 && rm -rf /var/lib/apt/lists/*

# Install LuaJIT - The just-in-time compiler for Lua
RUN wget http://luajit.org/download/LuaJIT-${LUAJIT_VERSION}.tar.gz \
 && tar -xzvf LuaJIT-${LUAJIT_VERSION}.tar.gz \
 && make -C LuaJIT-${LUAJIT_VERSION} \
 && make -C LuaJIT-${LUAJIT_VERSION} install \
 && rm LuaJIT-${LUAJIT_VERSION}.tar.gz \
 && rm -rf LuaJIT-${LUAJIT_VERSION}

# Install Nginx Development Kit to add generic function to Nginx core
RUN wget https://github.com/simpl/ngx_devel_kit/archive/v${NGINX_DEVEL_KIT_VERSION}.tar.gz -O ngx_devel_kit-${NGINX_DEVEL_KIT_VERSION}.tar.gz \
 && tar -xzvf ngx_devel_kit-${NGINX_DEVEL_KIT_VERSION}.tar.gz \
 && rm ngx_devel_kit-${NGINX_DEVEL_KIT_VERSION}.tar.gz

# Install Nginx Lua Module - adds Lua scriptability to Nginx server
RUN wget https://github.com/openresty/lua-nginx-module/archive/v${LUA_NGINX_MODULE_VERSION}.tar.gz -O lua-nginx-module-${LUA_NGINX_MODULE_VERSION}.tar.gz \
 && tar -xzvf lua-nginx-module-${LUA_NGINX_MODULE_VERSION}.tar.gz \
 && rm lua-nginx-module-${LUA_NGINX_MODULE_VERSION}.tar.gz

# Compile nginx from source
RUN wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz \
 && tar -xzvf nginx-${NGINX_VERSION}.tar.gz \
 && cd nginx-${NGINX_VERSION} \
 && export LUAJIT_LIB=/usr/local/lib \
 && export LUAJIT_INC=/usr/local/include/luajit-2.0 \
 && ./configure \
    --prefix=/etc/nginx \
    --sbin-path=/usr/sbin/nginx \
    --conf-path=/etc/nginx/nginx.conf \
    --pid-path=/var/run/nginx.pid \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --lock-path=/var/run/nginx.lock \
    --user=nginx \
    --group=nginx \
    --with-http_ssl_module \
    --with-cc-opt="-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2" \
    --with-ld-opt="-Wl,-z,relro -Wl,--as-needed,-rpath,${LUAJIT_LIB}" \
    --add-module=/ngx_devel_kit-${NGINX_DEVEL_KIT_VERSION} \
    --add-module=/lua-nginx-module-${LUA_NGINX_MODULE_VERSION} \
 && make -j2 \
 && make install \
 && ln -s ${NGINX_ROOT}/sbin/nginx /usr/local/sbin/nginx \
 && rm -f  /nginx-${NGINX_VERSION}.tar.gz \
 && rm -rf /nginx-${NGINX_VERSION} \
 && rm -rf /ngx_devel_kit-${NGINX_DEVEL_KIT_VERSION} \
 && rm -rf /lua-nginx-module-${LUA_NGINX_MODULE_VERSION}

# forward request and error logs to docker log collector
RUN ln -sf /dev/stderr /var/log/nginx/access.log \
 && ln -sf /dev/stderr /var/log/nginx/error.log

CMD ["nginx", "-g", "daemon off;"]
