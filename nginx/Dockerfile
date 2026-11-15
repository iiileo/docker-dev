FROM nginx:1.27-bookworm

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends vim wget procps \
    && groupadd -r -g 82 www \
    && useradd --system -u 82 -g www www \
    && mkdir -p /var/log/nginx \
    && rm -rf /var/lib/apt/lists/*

COPY nginx.conf /etc/nginx/nginx.conf
COPY snippets/ /etc/nginx/snippets/
COPY sites/ /etc/nginx/conf.d/

EXPOSE 80 443
