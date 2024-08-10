FROM ubuntu:18.04

RUN apt-get update && apt-get install -y curl=7.58.0 \
  --no-install-recommends \
  && apt-get clean && rm -rf /var/lib/apt/lists/*
COPY httpd.conf /etc/httpd/conf/httpd.conf

WORKDIR /root

ENTRYPOINT ["ping"]

CMD ["google.com"]