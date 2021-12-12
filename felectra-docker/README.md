**Felectra Docker cointaners документація**

**Вміст**
1. Огляд docker-compose.yml
   - tm_server
   - ipsec
   - connector_telegraf
2. Огляд Dockerfile контейнерів
   - tm_server
   - ipsec
   - connector_telegraf
3. Інструкція по установці проекта на пристрій використовуючи рішення Balena

**Огляд docker-compose.yml**
`version: '2'
services:

#TM Server
  tm_server:
    build:
      context: ./tm_cpps
      dockerfile: Dockerfile
    image: tm_cpps:latest
    container_name: tm_cpps
    restart: always
    volumes:
      - 'resin-data:/opt/tm_cpps/'
      - 'resin-data:/tmp/tm_cpps/'
      - 'resin-data:/opt/tm_cpps/cfg/'
      - 'resin-data:/sys/class/gpio/'
      - 'resin-data:/sys/firmware/devicetree/base/serial-number'
      - 'resin-data:/sys/firmware/devicetre'
      - 'resin-data:/proc/device-tree'
    labels:
      io.balena.features.balena-api: '1'
      io.balena.features.supervisor-api: '1'
      io.balena.features.balena-socket: '1'
      io.balena.features.kernel-modules: '1'
      io.balena.features.firmware: '1'
      io.balena.features.dbus: '1'
      io.balena.features.sysfs: '1'
      io.balena.features.procfs: '1'
      io.balena.features.journal-logs: '1'
      io.balena.update.strategy: download-then-kill
      io.balena.update.handover-timeout: ''
    ports:
      - "8081:8081"
      - "2404:2404"
    networks:
      - app-network

#IPSec
  ipsec:
    build:
      context: ./ipsec-client
      dockerfile: Dockerfile
    image: ipsec:latest
    container_name: ipsec
    restart: unless-stopped
    ports:
      - "500:500/udp"
      - "4500:4500/udp"
    environment:
      - VPN_SERVER_IP=${VPN_SERVER_IP}
      - VPN_PSEC_PSK=${VPN_PSEC_PSK}
      - VPN_USER=${VPN_USER}
      - VPN_PASSWORD=${VPN_PASSWORD}
      - VERBOSE=${VERBOSE}
    networks:
      - app-network

#Telegraf
  connector_telegraf:
    build:
      context: ./connector
      dockerfile: Dockerfile
    volumes:
      - 'resin-data:/opt/tm_cpps/'
    image: telegraf:latest
    restart: always
    labels:
      io.balena.features.balena-api: '1'
      io.balena.features.supervisor-api: '1'
      io.balena.features.balena-socket: '1'
      io.balena.features.kernel-modules: '1'
      io.balena.features.firmware: '1'
      io.balena.features.dbus: '1'
      io.balena.features.sysfs: '1'
      io.balena.features.procfs: '1'
      io.balena.features.journal-logs: '1'
      io.balena.update.strategy: download-then-kill
      io.balena.update.handover-timeout: ''
    privileged: true 
    ports:
      - "8080" 
    networks:
      - app-network

#Docker Networks
networks:
  app-network:
    driver: bridge
volumes:
    resin-data:`
