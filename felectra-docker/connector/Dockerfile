FROM balenalib/raspberrypi4-64-debian:buster

RUN apt update && apt upgrade -y && \
    apt install wget git -y

RUN curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
RUN echo "deb https://repos.influxdata.com/debian buster stable" | sudo tee /etc/apt/sources.list.d/influxdb.list

RUN printf "#!/bin/sh\nexit 0" > /usr/sbin/policy-rc.d
RUN chmod +x /usr/sbin/policy-rc.d

RUN sudo apt update && \
    sudo apt-get install -y telegraf

RUN RUNLEVEL=1 dpkg --configure -a

COPY telegraf.conf /etc/telegraf/telegraf.conf

ENV UDEV=1

CMD ["balena-idle"]
