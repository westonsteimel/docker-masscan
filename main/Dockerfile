ARG MASSCAN_VERSION="master"

FROM alpine:edge as builder

ARG MASSCAN_VERSION

RUN apk add --no-cache \
	ca-certificates \
	libpcap-dev

RUN set -x \
	&& apk add --no-cache --virtual .build-deps \
		build-base \
		clang \
		clang-dev \
		git \
		linux-headers \
	&& rm -rf /var/lib/apt/lists/* \
	&& git clone --depth 1 --branch "${MASSCAN_VERSION}" https://github.com/robertdavidgraham/masscan.git /usr/src/masscan \
	&& ( \
	cd /usr/src/masscan \
	&& make \
	&& make install \
	) \
	&& rm -rf /usr/src/masscan \
	&& apk del .build-deps

RUN addgroup masscan \
    && adduser -G masscan -s /bin/sh -D masscan

FROM alpine:edge

ARG MASSCAN_VERSION

RUN apk add --no-cache \
    libpcap-dev

COPY --from=builder /usr/bin/masscan /usr/bin/
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/ssl/certs /etc/ssl/certs

USER masscan

ENTRYPOINT [ "/usr/bin/masscan" ]

LABEL org.opencontainers.image.title="masscan" \
    org.opencontainers.image.description="masscan in Docker" \ 
    org.opencontainers.image.url="https://github.com/westonsteimel/docker-masscan" \ 
    org.opencontainers.image.source="https://github.com/westonsteimel/docker-masscan" \
    org.opencontainers.image.version="${MASSCAN_VERSION}" \
    version="${MASSCAN_VERSION}"
