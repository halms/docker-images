# syntax=docker/dockerfile:1

FROM dhi.io/alpine-base:3.24-dev@sha256:0164533a65c7d2326758ce453d0085a9f6f7542b06a7a3cc9c984a139bd46467 AS build
# 3.24-dev

RUN apk --no-cache add \
    libcap \
    unbound \
    && test -s /usr/share/dnssec-root/trusted-key.key

FROM dhi.io/alpine-base:3.24@sha256:40e5015ac7a0474495d0ee7b0583294b1ac14ad3a88a391fdeb777b9c8750777
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
