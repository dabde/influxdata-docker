FROM debian:bullseye-slim

RUN set -ex && \
    mkdir ~/.gnupg; \
    echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf; \
    apt-get update && apt-get install -y gnupg ca-certificates dirmngr --no-install-recommends && \
    rm -rf /var/lib/apt/lists/* && \
    for key in \
        05CE15085FC09D18E99EFB22684A14CF2582E0C5 ; \
    do \
        gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys "$key" ; \
    done

ENV CHRONOGRAF_VERSION 1.6.2
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" && \
    case "${dpkgArch##*-}" in \
      amd64) ARCH='amd64';; \
      arm64) ARCH='arm64';; \
      armhf) ARCH='armhf';; \
      armel) ARCH='armel';; \
      *)     echo "Unsupported architecture: ${dpkgArch}"; exit 1;; \
    esac && \
    set -x && \
    apt-get update && apt-get install -y ca-certificates curl --no-install-recommends && \
    rm -rf /var/lib/apt/lists/* && \
    curl -SLO "https://dl.influxdata.com/chronograf/releases/chronograf_${CHRONOGRAF_VERSION}_${ARCH}.deb.asc" && \
    curl -SLO "https://dl.influxdata.com/chronograf/releases/chronograf_${CHRONOGRAF_VERSION}_${ARCH}.deb" && \
    gpg --batch --verify chronograf_${CHRONOGRAF_VERSION}_${ARCH}.deb.asc chronograf_${CHRONOGRAF_VERSION}_${ARCH}.deb && \
    dpkg -i chronograf_${CHRONOGRAF_VERSION}_${ARCH}.deb && \
    rm -f chronograf_${CHRONOGRAF_VERSION}_${ARCH}.deb* && \
    apt-get purge -y --auto-remove $buildDeps

COPY LICENSE /usr/share/chronograf/LICENSE
COPY agpl-3.0.md /usr/share/chronograf/agpl-3.0.md

EXPOSE 8888

VOLUME /var/lib/chronograf

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["chronograf"]
