FROM ubuntu:19.10
MAINTAINER forsrc@gmail.com

ENV DEBIAN_FRONTEND=noninteractive


ENV container docker
EXPOSE 389 636 9090

RUN apt-get update
RUN apt-get install -y systemd 389-ds
RUN apt-get clean

ARG USER=dirsrv
ARG ROOT_PASSWD
ARG ROOT_PASSWD=${ROOT_PASSWD:-forsrc}
ARG SUFFIX
ARG SUFFIX=${SUFFIX:-dc=forsrc,dc=com}
ARG INSTANCE_NAME
ARG INSTANCE_NAME=${INSTANCE_NAME:-forsrc.com}

RUN mkdir -p /home/$USER/ 

RUN mkdir -p /data/ask-password && ln -s /data/ask-password /var/run/systemd/ask-password

RUN dscreate create-template | sed \
    -e "s/;instance_name = localhost/instance_name = ${INSTANCE_NAME}/g" \
    -e "s/;root_password = .*/root_password = ${ROOT_PASSWD}/g" \
    -e "s/;suffix = .*/suffix = ${SUFFIX}/g" \
    -e "s/;selinux = .*/selinux = False/g" \
    > /home/$USER/ds.inf

RUN sh -c "dscreate from-file /home/$USER/ds.inf; exit 0"

RUN sed -i 's/^SELINUX=.*$/SELINUX=disabled/g' /etc/selinux/config 

RUN systemctl enable dirsrv@$INSTANCE_NAME

CMD ["/sbin/init"]
