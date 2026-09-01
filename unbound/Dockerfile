# syntax=docker/dockerfile:1

FROM dhi.io/alpine-base:3.24-dev@sha256:ad660e4218bb22f3d03dbf0c8cb483f7429bbc6ed7dead7d602b8f6cd35bbeb5 AS build
# 3.24-dev

RUN apk --no-cache add \
    libcap \
    unbound \
    && test -s /usr/share/dnssec-root/trusted-key.key

FROM dhi.io/alpine-base:3.24@sha256:c83afceb9027a70719ea5ed916754a94ecdbe33a64cb8bcbb4da9fbffaefe7b0
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
