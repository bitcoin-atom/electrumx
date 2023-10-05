# example of Dockerfile that installs electrumx 1.16.0
# ENV variables can be overriden on the `docker run` command

FROM python:3.9.16-bullseye

# Install the libs needed by rocksdb (including development headers)
RUN apt-get update && \
    apt-get -y --no-install-recommends install librocksdb-dev libsnappy-dev libbz2-dev libz-dev liblz4-dev && \
    rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir Cython==0.29.21 && \
    pip install --no-cache-dir python-rapidjson==1.11 && \
    pip install --no-cache-dir python-rocksdb==0.7.0

# Install the libs needed by rocksdb (no development headers or statics)
RUN apt-get update && \
    apt-get -y --no-install-recommends install librocksdb6.11 libsnappy1v5 libbz2-1.0 zlib1g liblz4-1 && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /

RUN apt-get update && \
    apt-get install -y python3 python3-pip git && \
    pip install aiohttp pylru plyvel websockets python-rocksdb uvloop && \
    git clone https://github.com/bitcoin-atom/electrumx.git && \
    cd electrumx && \
    python setup.py install && \
    rm -rf /tmp/*

ENV HOME /data
ENV DB_DIRECTORY=/data
ENV SSL_CERTFILE /electrumx/ssl/electrumx.crt
ENV SSL_KEYFILE /electrumx/ssl/electrumx.key
ENV ALLOW_ROOT=1
ENV DB_ENGINE=rocksdb
ENV MAX_SEND=10000000
ENV BANDWIDTH_UNIT_COST=50000
ENV CACHE_MB=2000

RUN mkdir -p /electrumx/ssl
RUN mkdir -p "$DB_DIRECTORY" && ulimit -n 1048576
VOLUME ["/data"]
WORKDIR /data

RUN openssl req -newkey rsa:2048 -sha256 -nodes -x509 -days 1825 -subj "/O=ElectrumX" -keyout "/electrumx/ssl/electrumx.key" -out "/electrumx/ssl/electrumx.crt"

CMD ["/electrumx/electrumx_server"]
