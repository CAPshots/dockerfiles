FROM golang:1.18.3-alpine3.16@sha256:7cc62574fcf9c5fb87ad42a9789d5539a6a085971d58ee75dd2ee146cb8a8695 AS build

WORKDIR /go/src/github.com/binwiederhier/ntfy

ARG BUILD_VERSION
ARG ARCHIVE_URL=https://github.com/binwiederhier/ntfy/archive/

ENV GO111MODULE on
ENV CGO_ENABLED 1

COPY . .

RUN test -n "${BUILD_VERSION}" \
	&& apk update \
	&& apk add --no-cache ca-certificates curl git gcc build-base \
	&& update-ca-certificates \
	&& curl -L "${ARCHIVE_URL}${BUILD_VERSION}.tar.gz" -o /tmp/ntfy.tar.gz \
	&& tar xzf /tmp/ntfy.tar.gz --strip 1 -C /go/src/github.com/binwiederhier/ntfy \ 
	&& mkdir server/docs \ 
	&& touch server/docs/dummy \ 
	&& mkdir server/site \ 
	&& touch server/site/dummy \
	&& go build -o ntfy -v -trimpath -buildmode=pie -ldflags="-s -w -X main.version=${BUILD_VERSION}" .

# Validation check
#RUN cp ntfy /go/bin/ntfy



# ----------------------------------------------------------------------------



#FROM scratch
FROM alpine:3.16.0@sha256:686d8c9dfa6f3ccfc8230bc3178d23f84eeaf7e457f36f271ab1acc53015037c

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/ntfy"
LABEL org.opencontainers.image.description="ntfy is a simple HTTP-based pub-sub notification service"

#COPY --from=build /go/bin/ntfy /ntfy
COPY --from=build /go/src/github.com/binwiederhier/ntfy/ntfy /usr/local/bin/ntfy

ENTRYPOINT ["/usr/local/bin/ntfy"]

