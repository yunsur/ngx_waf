ARG DEBIAN_VERSION=12

FROM debian:${DEBIAN_VERSION} AS builder

ARG NGINX_VERSION=1.26.3

ARG NGX_WAF_VER=10.1.2
ARG LIB_CJSON_VER=1.7.18
ARG LIB_UTHASH_VER=2.3.0

WORKDIR /usr/local/src/

RUN set -xe \
    && apt-get update -y \
    && apt-get --no-install-recommends --no-install-suggests -y install \
        build-essential ca-certificates git wget lsb-release \
        libpcre2-dev libpcre3-dev zlib1g-dev libssl-dev libxml2-utils \
        xsltproc mercurial devscripts fakeroot debhelper dpkg-dev quilt \
        libparse-recdescent-perl \
        libmodsecurity-dev libcurl4-openssl-dev libmaxminddb-dev libsodium-dev \
    && rm -rf /var/lib/apt/lists/*

RUN set -xe \
    && wget https://github.com/nginx/pkg-oss/archive/refs/tags/${NGINX_VERSION}-1.tar.gz -O pkg-oss.tar.gz \
    && tar -xzf pkg-oss.tar.gz \
    && mv pkg-oss-${NGINX_VERSION}-1 pkg-oss \
    && rm pkg-oss.tar.gz

RUN set -xe \
    && git clone -b v${NGX_WAF_VER} https://github.com/ADD-SP/ngx_waf.git /usr/local/src/ngx_waf

RUN set -xe \
    && git clone -b v${LIB_CJSON_VER} https://github.com/DaveGamble/cJSON.git /usr/local/src/ngx_waf/lib/cjson

RUN set -xe \
    && git clone -b v${LIB_UTHASH_VER} https://github.com/troydhanson/uthash.git /usr/local/src/ngx_waf/lib/uthash

RUN set -xe \
    && /usr/local/src/pkg-oss/build_module.sh -s -v ${NGINX_VERSION} -V ${NGX_WAF_VER} -n waf /usr/local/src/ngx_waf

FROM scratch

COPY --from=builder /usr/local/src/build-module-artifacts/ /
