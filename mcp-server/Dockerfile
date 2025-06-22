FROM python:3-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    supervisor \
    ca-certificates \
    git \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /var/log/supervisor

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

RUN pip install --no-cache-dir \
    uv \
    mcpo \
    mcp-server-time


CMD ["/usr/bin/supervisord"]