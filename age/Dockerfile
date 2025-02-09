FROM golang:1.18.3-alpine3.16@sha256:7cc62574fcf9c5fb87ad42a9789d5539a6a085971d58ee75dd2ee146cb8a8695 AS build

WORKDIR /go/src/github.com/FiloSottile/age/

ARG BUILD_VERSION
ARG ARCHIVE_URL=https://github.com/FiloSottile/age/archive/
ARG GIT_URL=https://github.com/FiloSottile/age.git

ENV GO111MODULE on
ENV CGO_ENABLED 0

RUN test -n "${BUILD_VERSION}" \
	&& apk update \
	&& apk add --no-cache ca-certificates curl git gcc musl-dev \
	&& update-ca-certificates \
	&& git clone --depth 1 ${GIT_URL} /go/src/github.com/FiloSottile/age \
        && cd /go/src/github.com/FiloSottile/age \
	&& go build -o . -v -trimpath -ldflags="-s -w -X main.Version=${BUILD_VERSION}" filippo.io/age/cmd/... 

WORKDIR /config


# ----------------------------------------------------------------------------


FROM scratch
#FROM alpine:3.14

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/age"
LABEL org.opencontainers.image.description="age is a simple, modern and secure file encryption tool, format, and Go library"

COPY --from=build /etc/ssl/certs /etc/ssl/certs

COPY --from=build /go/src/github.com/FiloSottile/age/age /usr/local/bin/age
COPY --from=build /go/src/github.com/FiloSottile/age/age-keygen /usr/local/bin/age-keygen
COPY --from=build /config /config

# EXPOSE
# ENV
# STOPSIGNAL
HEALTHCHECK NONE
# USER

CMD ["/usr/local/bin/age"]
