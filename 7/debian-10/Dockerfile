FROM docker.io/bitnami/minideb:buster
LABEL maintainer "Adcuratio <uddipta.poddar@adcuratio.com>"

ENV HOME="/" \
    OS_ARCH="amd64" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux"

ARG JAVA_EXTRA_SECURITY_DIR="/bitnami/java/extra-security"

COPY prebuildfs /
# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl gzip libc6 procps tar zlib1g

RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "java" "11.0.13-0" --checksum 9552e53ccd3b0ec85a82c31c26a1462260e28b7c58770a5c53560afb02ec66d9
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "logstash" "7.15.2-0" --checksum 22d4404d0079f5a4f7355b8b457e2d9c9b878f72640189a0ae9a085154d9e78c
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.14.0-0" --checksum 3e6fc37ca073b10a73a804d39c2f0c028947a1a596382a4f8ebe43dfbaa3a25e
RUN chmod g+rwX /opt/bitnami

COPY rootfs /
RUN /opt/bitnami/scripts/logstash/postunpack.sh
RUN /opt/bitnami/scripts/java/postunpack.sh
ENV BITNAMI_APP_NAME="logstash" \
    BITNAMI_IMAGE_VERSION="7.15.2-debian-10-r8" \
    JAVA_HOME="/opt/bitnami/java" \
    PATH="/opt/bitnami/java/bin:/opt/bitnami/logstash/bin:/opt/bitnami/common/bin:$PATH"

EXPOSE 8080 5672

WORKDIR /opt/bitnami/logstash

# create user and usergroup
# get username & password on build
ARG username
RUN echo "Admin Username: $username"

ARG password
RUN echo "Admin Password: $password"

RUN useradd -M --system --shell /bin/bash --password $(echo $password | openssl passwd -1 -stdin) $username
RUN usermod -aG root,sudo $username

RUN mkdir /opt/bitnami/apilogs/ /opt/bitnami/ssl/ && chown $username:$username -R /opt/bitnami/apilogs/ /opt/bitnami/ssl/ && chmod -R 0700 /opt/bitnami/apilogs/ /opt/bitnami/ssl/


USER $username

ENTRYPOINT [ "/opt/bitnami/scripts/logstash/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/logstash/run.sh" ]

