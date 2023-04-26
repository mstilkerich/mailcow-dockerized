# go 1.19 is required at this point; with go 1.20 weird errors occur
# See https://github.com/freswa/dovecot-xaps-daemon/issues/24
FROM golang:1.19-alpine3.17 AS builder
WORKDIR /app
COPY . .

RUN \
  apk update && \
  apk add git && \
  mkdir dovecot-xaps-daemon && \
  cd dovecot-xaps-daemon && \
  git init . && \
  git remote add origin https://github.com/mstilkerich/dovecot-xaps-daemon.git && \
  git fetch --depth 1 origin 73bab1b28ccf6573778678b072e43b5f72d34fca && \
  git checkout FETCH_HEAD && \
  go build ./cmd/xapsd

#go build -ldflags "-linkmode external -extldflags -static" ./cmd/xapsd && \

# Build image with only xapsd binary
FROM alpine:3.17
COPY --from=builder /app/dovecot-xaps-daemon/xapsd /bin/xapsd
RUN adduser -g "xapsd user" -h /dev/null -u 11619 -G nogroup -s /bin/false -D xapsd && \
  mkdir -m 700 /var/lib/xapsd && \
  chown xapsd:nogroup /var/lib/xapsd
USER xapsd
VOLUME /var/lib/xapsd
ENTRYPOINT ["/bin/xapsd"]
