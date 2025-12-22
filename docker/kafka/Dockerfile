ARG scala_version=2.13
ARG kafka_version=3.9.1 # renovate: datasource=github-tags depName=apache/kafka
ARG java_version=21

FROM alpine:latest AS kafka_dist

ARG scala_version
ARG kafka_version
ARG kafka_distro_base_url=https://archive.apache.org/dist/kafka

ENV kafka_distro=kafka_$scala_version-$kafka_version.tgz
ENV kafka_distro_asc=$kafka_distro.asc

WORKDIR /var/tmp

# Download, verify, extract and clean up Kafka in single layer
RUN apk add --no-cache gnupg wget && \
    wget -q $kafka_distro_base_url/$kafka_version/$kafka_distro && \
    wget -q $kafka_distro_base_url/$kafka_version/$kafka_distro_asc && \
    wget -q $kafka_distro_base_url/KEYS && \
    gpg --import KEYS && \
    gpg --verify $kafka_distro_asc $kafka_distro && \
    tar -xzf $kafka_distro && \
    rm -rf kafka_$scala_version-$kafka_version/bin/windows \
           kafka_$scala_version-$kafka_version/site-docs \
           kafka_$scala_version-$kafka_version/libs/zookeeper-* \
           $kafka_distro $kafka_distro_asc KEYS && \
    zookeeper_version=3.8.5 && wget -qO- https://archive.apache.org/dist/zookeeper/zookeeper-$zookeeper_version/apache-zookeeper-$zookeeper_version-bin.tar.gz | tar -xzCkafka_$scala_version-$kafka_version/libs --strip-components 2 apache-zookeeper-$zookeeper_version-bin/lib/zookeeper-jute-$zookeeper_version.jar apache-zookeeper-$zookeeper_version-bin/lib/zookeeper-$zookeeper_version.jar && \
    echo IyBMaWNlbnNlZCB0byB0aGUgQXBhY2hlIFNvZnR3YXJlIEZvdW5kYXRpb24gKEFTRikgdW5kZXIgb25lIG9yIG1vcmUKIyBjb250cmlidXRvciBsaWNlbnNlIGFncmVlbWVudHMuICBTZWUgdGhlIE5PVElDRSBmaWxlIGRpc3RyaWJ1dGVkIHdpdGgKIyB0aGlzIHdvcmsgZm9yIGFkZGl0aW9uYWwgaW5mb3JtYXRpb24gcmVnYXJkaW5nIGNvcHlyaWdodCBvd25lcnNoaXAuCiMgVGhlIEFTRiBsaWNlbnNlcyB0aGlzIGZpbGUgdG8gWW91IHVuZGVyIHRoZSBBcGFjaGUgTGljZW5zZSwgVmVyc2lvbiAyLjAKIyAodGhlICJMaWNlbnNlIik7IHlvdSBtYXkgbm90IHVzZSB0aGlzIGZpbGUgZXhjZXB0IGluIGNvbXBsaWFuY2Ugd2l0aAojIHRoZSBMaWNlbnNlLiAgWW91IG1heSBvYnRhaW4gYSBjb3B5IG9mIHRoZSBMaWNlbnNlIGF0CiMKIyAgICBodHRwOi8vd3d3LmFwYWNoZS5vcmcvbGljZW5zZXMvTElDRU5TRS0yLjAKIwojIFVubGVzcyByZXF1aXJlZCBieSBhcHBsaWNhYmxlIGxhdyBvciBhZ3JlZWQgdG8gaW4gd3JpdGluZywgc29mdHdhcmUKIyBkaXN0cmlidXRlZCB1bmRlciB0aGUgTGljZW5zZSBpcyBkaXN0cmlidXRlZCBvbiBhbiAiQVMgSVMiIEJBU0lTLAojIFdJVEhPVVQgV0FSUkFOVElFUyBPUiBDT05ESVRJT05TIE9GIEFOWSBLSU5ELCBlaXRoZXIgZXhwcmVzcyBvciBpbXBsaWVkLgojIFNlZSB0aGUgTGljZW5zZSBmb3IgdGhlIHNwZWNpZmljIGxhbmd1YWdlIGdvdmVybmluZyBwZXJtaXNzaW9ucyBhbmQKIyBsaW1pdGF0aW9ucyB1bmRlciB0aGUgTGljZW5zZS4KCiMgVW5zcGVjaWZpZWQgbG9nZ2VycyBhbmQgbG9nZ2VycyB3aXRoIGFkZGl0aXZpdHk9dHJ1ZSBvdXRwdXQgdG8gc2VydmVyLmxvZyBhbmQgc3Rkb3V0CiMgTm90ZSB0aGF0IElORk8gb25seSBhcHBsaWVzIHRvIHVuc3BlY2lmaWVkIGxvZ2dlcnMsIHRoZSBsb2cgbGV2ZWwgb2YgdGhlIGNoaWxkIGxvZ2dlciBpcyB1c2VkIG90aGVyd2lzZQpsb2c0ai5yb290TG9nZ2VyPUlORk8sIHN0ZG91dAoKbG9nNGouYXBwZW5kZXIuc3Rkb3V0PW9yZy5hcGFjaGUubG9nNGouQ29uc29sZUFwcGVuZGVyCmxvZzRqLmFwcGVuZGVyLnN0ZG91dC5sYXlvdXQ9b3JnLmFwYWNoZS5sb2c0ai5QYXR0ZXJuTGF5b3V0CmxvZzRqLmFwcGVuZGVyLnN0ZG91dC5sYXlvdXQuQ29udmVyc2lvblBhdHRlcm49WyVkXSAlcCAlbSAoJWMpJW4KCiMgQ2hhbmdlIHRoZSBsaW5lIGJlbG93IHRvIGFkanVzdCBaSyBjbGllbnQgbG9nZ2luZwpsb2c0ai5sb2dnZXIub3JnLmFwYWNoZS56b29rZWVwZXI9SU5GTwoKIyBDaGFuZ2UgdGhlIHR3byBsaW5lcyBiZWxvdyB0byBhZGp1c3QgdGhlIGdlbmVyYWwgYnJva2VyIGxvZ2dpbmcgbGV2ZWwgKG91dHB1dCB0byBzZXJ2ZXIubG9nIGFuZCBzdGRvdXQpCmxvZzRqLmxvZ2dlci5rYWZrYT1JTkZPCmxvZzRqLmxvZ2dlci5vcmcuYXBhY2hlLmthZmthPUlORk8KCiMgQ2hhbmdlIHRvIERFQlVHIG9yIFRSQUNFIHRvIGVuYWJsZSByZXF1ZXN0IGxvZ2dpbmcKbG9nNGoubG9nZ2VyLmthZmthLnJlcXVlc3QubG9nZ2VyPVdBUk4KCiMgVW5jb21tZW50IHRoZSBsaW5lcyBiZWxvdyBhbmQgY2hhbmdlIGxvZzRqLmxvZ2dlci5rYWZrYS5uZXR3b3JrLlJlcXVlc3RDaGFubmVsJCB0byBUUkFDRSBmb3IgYWRkaXRpb25hbCBvdXRwdXQKIyByZWxhdGVkIHRvIHRoZSBoYW5kbGluZyBvZiByZXF1ZXN0cwojbG9nNGoubG9nZ2VyLmthZmthLm5ldHdvcmsuUHJvY2Vzc29yPVRSQUNFLCByZXF1ZXN0QXBwZW5kZXIKI2xvZzRqLmxvZ2dlci5rYWZrYS5zZXJ2ZXIuS2Fma2FBcGlzPVRSQUNFLCByZXF1ZXN0QXBwZW5kZXIKIwpsb2c0ai5sb2dnZXIua2Fma2EubmV0d29yay5SZXF1ZXN0Q2hhbm5lbCQ9V0FSTgoKbG9nNGoubG9nZ2VyLmthZmthLmNvbnRyb2xsZXI9REVCVUcKCgpsb2c0ai5sb2dnZXIua2Fma2EubG9nLkxvZ0NsZWFuZXI9SU5GTwoKCmxvZzRqLmxvZ2dlci5zdGF0ZS5jaGFuZ2UubG9nZ2VyPUlORk8KCiMgQWNjZXNzIGRlbmlhbHMgYXJlIGxvZ2dlZCBhdCBJTkZPIGxldmVsLCBjaGFuZ2UgdG8gREVCVUcgdG8gYWxzbyBsb2cgYWxsb3dlZCBhY2Nlc3Nlcwpsb2c0ai5sb2dnZXIua2Fma2EuYXV0aG9yaXplci5sb2dnZXI9SU5GTwo=|base64 -d >kafka_$scala_version-$kafka_version/config/log4j.properties


# backported from https://github.com/docker-library/openjdk/blob/master/18/jdk/slim-bullseye/Dockerfile
FROM debian:bullseye-slim

ARG scala_version
ARG kafka_version
ARG java_version
ARG VERSION
ARG BUILT_AT
ARG COMMIT

RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		ca-certificates p11-kit \
	; \
	rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/local/openjdk-${java_version}
ENV PATH=$JAVA_HOME/bin:$PATH

# Default to UTF-8 file.encoding
ENV LANG=C.UTF-8
ENV JAVA_VERSION=${java_version}

RUN set -eux; \
	\
	arch="$(dpkg --print-architecture)"; \
	case "$arch" in \
		'amd64') \
			downloadUrl="https://download.oracle.com/java/${java_version}/latest/jdk-${java_version}_linux-x64_bin.tar.gz"; \
			downloadSha256="https://download.oracle.com/java/${java_version}/latest/jdk-${java_version}_linux-x64_bin.tar.gz.sha256"; \
			;; \
		'arm64') \
			downloadUrl="https://download.oracle.com/java/${java_version}/latest/jdk-${java_version}_linux-aarch64_bin.tar.gz"; \
			downloadSha256="https://download.oracle.com/java/${java_version}/latest/jdk-${java_version}_linux-aarch64_bin.tar.gz.sha256"; \
			;; \
		*) echo >&2 "error: unsupported architecture: '$arch'"; exit 1 ;; \
	esac; \
	\
	savedAptMark="$(apt-mark showmanual)"; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		wget \
	; \
	rm -rf /var/lib/apt/lists/*; \
	\
	wget --progress=dot:giga -O openjdk.tgz "$downloadUrl"; \
    SHA256=$(wget -qO- $downloadSha256) ; \
	echo "$SHA256 *openjdk.tgz" | sha256sum --strict --check -; \
	\
	mkdir -p "$JAVA_HOME"; \
	tar --extract \
		--file openjdk.tgz \
		--directory "$JAVA_HOME" \
		--strip-components 1 \
		--no-same-owner \
	; \
	rm openjdk.tgz*; \
	\
# Remove JMODs and src.zip (not needed for runtime, only for development)
	rm -rf "$JAVA_HOME/jmods" "$JAVA_HOME/lib/src.zip"; \
	\
	apt-mark auto '.*' > /dev/null; \
	[ -z "$savedAptMark" ] || apt-mark manual $savedAptMark > /dev/null; \
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	\
# update "cacerts" bundle to use Debian's CA certificates (and make sure it stays up-to-date with changes to Debian's store)
# see https://github.com/docker-library/openjdk/issues/327
#     http://rabexc.org/posts/certificates-not-working-java#comment-4099504075
#     https://salsa.debian.org/java-team/ca-certificates-java/blob/3e51a84e9104823319abeb31f880580e46f45a98/debian/jks-keystore.hook.in
#     https://git.alpinelinux.org/aports/tree/community/java-cacerts/APKBUILD?id=761af65f38b4570093461e6546dcf6b179d2b624#n29
	{ \
		echo '#!/usr/bin/env bash'; \
		echo 'set -Eeuo pipefail'; \
		echo 'trust extract --overwrite --format=java-cacerts --filter=ca-anchors --purpose=server-auth "$JAVA_HOME/lib/security/cacerts"'; \
	} > /etc/ca-certificates/update.d/docker-openjdk; \
	chmod +x /etc/ca-certificates/update.d/docker-openjdk; \
	/etc/ca-certificates/update.d/docker-openjdk; \
	\
# https://github.com/docker-library/openjdk/issues/331#issuecomment-498834472
	find "$JAVA_HOME/lib" -name '*.so' -exec dirname '{}' ';' | sort -u > /etc/ld.so.conf.d/docker-openjdk.conf; \
	ldconfig; \
	\
# https://github.com/docker-library/openjdk/issues/212#issuecomment-420979840
# https://openjdk.java.net/jeps/341
	java -Xshare:dump; \
	\
# basic smoke test
	fileEncoding="$(echo 'System.out.println(System.getProperty("file.encoding"))' | jshell -s -)"; [ "$fileEncoding" = 'UTF-8' ]; rm -rf ~/.java; \
	javac --version; \
	java --version

ENV KAFKA_VERSION=$kafka_version \
    SCALA_VERSION=$scala_version \
    KAFKA_HOME=/opt/kafka

ENV PATH=${PATH}:${KAFKA_HOME}/bin

# Install minimal dependencies, setup Kafka, and clean up in single layer
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    mkdir -p ${KAFKA_HOME}/libs/extensions

COPY --from=kafka_dist /var/tmp/kafka_$scala_version-$kafka_version ${KAFKA_HOME}

# Optimize Kafka installation: remove unnecessary files and set permissions
RUN chmod a+x ${KAFKA_HOME}/bin/*.sh && \
    chmod g+rwX ${KAFKA_HOME} ${KAFKA_HOME}/libs/extensions

# Add metadata labels
LABEL org.opencontainers.image.title="Apache Kafka"
LABEL org.opencontainers.image.description="Apache Kafka with Oracle JDK ${java_version}"
LABEL org.opencontainers.image.vendor="Adobe"
LABEL org.opencontainers.image.licenses="Apache-2.0"
LABEL org.opencontainers.image.source="https://github.com/adobe/koperator"
LABEL org.opencontainers.image.documentation="https://github.com/adobe/koperator/blob/main/docker/kafka/README.md"
LABEL org.opencontainers.image.version="${VERSION}"
LABEL org.opencontainers.image.created="${BUILT_AT}"
LABEL org.opencontainers.image.revision="${COMMIT}"

CMD ["kafka-server-start.sh"]
