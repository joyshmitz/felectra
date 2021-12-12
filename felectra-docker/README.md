**Felectra Docker cointaners документація**

**Вміст**
1. Огляд docker-compose.yml

2. Огляд Dockerfile контейнерів
   - tm_server
   - ipsec
   - connector_telegraf
3. Інструкція по установці проекта на пристрій використовуючи рішення Balena

**Огляд docker-compose.yml**
```
version: '2'
services:
``
#TM Server
  tm_server:
    build:`
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
    driver: overlay
volumes:
    resin-data:
```
Використана версія Docker Compose 2. Використано режим мережі в Docker overlay для розподілення мережі між декількома вузлами. Дзінатись більше [за посиланням на документацію Docker](https://docs.docker.com/network/overlay/).

**Огляд Dockerfile контейнерів**
**TM Server**
```
FROM balenalib/aarch64-debian:buster-build
LABEL io.balena.device-type="raspberrypi4-64"
RUN echo "deb http://archive.raspberrypi.org/debian buster main ui" >>  /etc/apt/sources.list.d/raspi.list \
	&& apt-key adv --batch --keyserver keyserver.ubuntu.com  --recv-key 0x82B129927FA3303E

RUN apt-get update && apt-get install -y --no-install-recommends \
		less \
		libraspberrypi-bin \
		kmod \
		nano \
		net-tools \
		ifupdown \
		iputils-ping \
		i2c-tools \
		usbutils 

ADD tm_cpps_RPi4b64_13633.deb /tmp/1/
ADD start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container

RUN dpkg -i /tmp/1/tm_cpps_RPi4b64_13633.deb
RUN rm /tmp/tm_cpps/tm_server.pid

ENTRYPOINT ["start-container"]
```
Для створення Docker контейнеру для програми TM Server було використано образ balenalib/aarch64-debian:buster-build. 

```
RUN echo "deb http://archive.raspberrypi.org/debian buster main ui" >>  /etc/apt/sources.list.d/raspi.list \
	&& apt-key adv --batch --keyserver keyserver.ubuntu.com  --recv-key 0x82B129927FA3303E

RUN apt-get update && apt-get install -y --no-install-recommends \
		less \
		libraspberrypi-bin \
		kmod \
		nano \
		net-tools \
		ifupdown \
		iputils-ping \
		i2c-tools \
		usbutils 
```
В даному блоці команд для контейнера Docker комнада ``RUN`` відповідає за запуск консольних команд Linux.
```
ADD tm_cpps_RPi4b64_13633.deb /tmp/1/
ADD start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container
```
В блоці команд наведеному вище відбувається поміщення .deb пакету tm_cpps_RPi4b64_13633.deb з хоста в директорію /tmp/1/ в Docker контейнер за допомогою команди ``ADD tm_cpps_RPi4b64_13633.deb /tmp/1/``. Також за допомогою команди ``ADD start-container /usr/local/bin/start-container`` поміщається скрипт start-container. 
```
RUN dpkg -i /tmp/1/tm_cpps_RPi4b64_13633.deb
RUN rm /tmp/tm_cpps/tm_server.pid
```
Даний блок коду відповідає за інсталяцію пакету tm_cpps_RPi4b64_13633.deb і видалення pid процесу програми TM Server при збірці Docker контейнера.
