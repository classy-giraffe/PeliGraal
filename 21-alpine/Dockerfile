FROM alpine:latest

ARG TARGETPLATFORM
ARG JAVA_VERSION=21
LABEL AUTHOR="Tommaso Chiti"
LABEL MAINTAINER="chitilivorno@gmail.com"

# Default to UTF-8 file.encoding
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

RUN apk add --no-cache bash curl git tar openssl sqlite ca-certificates fontconfig tzdata iproute2 \
  && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
  && case ${TARGETPLATFORM} in \
         "linux/amd64")  ARCH=x64  ;; \
         "linux/arm64")  ARCH=aarch64  ;; \
    esac \
  && curl --retry 3 -Lfso /tmp/graalvm.tar.gz https://download.oracle.com/graalvm/${JAVA_VERSION}/latest/graalvm-jdk-${JAVA_VERSION}_linux-${ARCH}_bin.tar.gz \
  && curl --retry 3 -Lfso /tmp/graalvm.tar.gz.sha256 https://download.oracle.com/graalvm/${JAVA_VERSION}/latest/graalvm-jdk-${JAVA_VERSION}_linux-${ARCH}_bin.tar.gz.sha256 \
  && echo "$(cat /tmp/graalvm.tar.gz.sha256)  /tmp/graalvm.tar.gz" | sha256sum -c - \
  && echo "Hash verification succeeded!" \
  && mkdir -p /opt/java/graalvm \
  && cd /opt/java/graalvm \
  && tar -xf /tmp/graalvm.tar.gz --strip-components=1 \
  && export PATH="/opt/java/graalvm/bin:$PATH" \
  && rm -rf /tmp/graalvm.tar.gz /tmp/graalvm.tar.gz.sha256

ENV JAVA_HOME=/opt/java/graalvm \
    PATH="/opt/java/graalvm/bin:$PATH"

# Step 2 - add pterodactyl stuff
RUN adduser -h /home/container -D container

USER        container
ENV         USER=container HOME=/home/container

WORKDIR     /home/container

COPY        ./../entrypoint.sh /entrypoint.sh

CMD         ["/bin/bash", "/entrypoint.sh"]