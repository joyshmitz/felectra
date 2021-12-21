FROM balenalib/raspberrypi4-64-alpine

RUN apk add strongswan xl2tpd ppp; rm -rf /var/cache/apk/*

ADD conf/ conf/docker-entrypoint.sh /opt/src/

ENTRYPOINT ["/opt/src/docker-entrypoint.sh"]
