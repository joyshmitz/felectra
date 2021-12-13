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
