# syntax=docker/dockerfile:1

FROM dhi.io/alpine-base:3.24-dev@sha256:bb33817809b675cd6da270279be956c9fc2c1471bc0b5b2a84ece82d93b6a39d AS build
# 3.24-dev

RUN apk --no-cache add \
    libcap \
    unbound \
    && test -s /usr/share/dnssec-root/trusted-key.key

FROM dhi.io/alpine-base:3.24@sha256:32e94d5fda692447a1ca48ff8910e5be4657053ba74744cd6b0cf6ed816c6cbf
# 3.24

COPY --from=build /usr/lib/ /usr/lib/
COPY --from=build /usr/sbin/unbound* /usr/sbin/
COPY --from=build /usr/share/dnssec-root/ /usr/share/dnssec-root/
COPY --from=build /usr/share/unbound/ /usr/share/unbound/
COPY --chown=65532:65532 --from=build /etc/unbound/ /etc/unbound/

USER 0
RUN --mount=from=build,source=/usr/sbin/setcap,target=/usr/sbin/setcap \
    setcap CAP_NET_BIND_SERVICE=+ep /usr/sbin/unbound

EXPOSE 53/udp 53/tcp

USER nonroot
ENTRYPOINT ["/usr/sbin/unbound"]
CMD ["-c", "/etc/unbound/unbound.conf", "-d", "-v"]
