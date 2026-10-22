ARG APP_ROOT=/app

# skopeo list-tags docker://golang | jq -r '.Tags[]' | grep -E '^[0-9]+\.[0-9]+.[0-9]+-alpine' | sort -V
# skopeo inspect --raw docker://golang:1.27.2-alpine3.24 | sha256sum
FROM golang:1.27.3-alpine3.24@sha256:b8c9b82affbbb82a020bbcf998dad2ba9516944ac7e883524642a481a9505e9c AS builder
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN apk --update --no-cache add ca-certificates
RUN go install go.opentelemetry.io/collector/cmd/builder@v0.163.0

COPY cmd/ ./cmd
RUN builder --config cmd/otelcontribcol/builder-config.yaml --verbose

# skopeo list-tags docker://alpine | jq -r '.Tags[]' | grep -E '^[0-9]+\.[0-9]+.[0-9]+' | sort -V
# skopeo inspect --raw docker://alpine:3.24.2 | sha256sum
FROM alpine:3.24.3@sha256:b3413572228e34bd1c9245f5565e6cd127005655e8a7899aef33d8e83255c9e6 AS runner
ARG APP_ROOT
ARG USER_UID=10001
ARG USER_GID=10001

USER ${USER_UID}:${USER_GID}

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder ${APP_ROOT}/cmd/otelcontribcol/otelcontribcol /
ENTRYPOINT ["/otelcontribcol"]
CMD ["--config", "/etc/otel/config.yaml"]
