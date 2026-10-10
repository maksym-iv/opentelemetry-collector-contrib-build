ARG APP_ROOT=/app

FROM golang:1.27.2-alpine3.24@sha256:f92b6ef800e499660581efdabdf25d9d817a9d124eaf900924f0504e7e27e12d AS builder
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN apk --update --no-cache add ca-certificates
RUN go install go.opentelemetry.io/collector/cmd/builder@v0.162.0

COPY cmd/ ./cmd
RUN builder --config cmd/otelcontribcol/builder-config.yaml --verbose

FROM alpine:3.24@sha256:294b683cb724975bec92580e1e685676bd4b50bda910ddb8c51d4cabeaec77e6 AS runner
ARG APP_ROOT
ARG USER_UID=10001
ARG USER_GID=10001

USER ${USER_UID}:${USER_GID}

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder ${APP_ROOT}/cmd/otelcontribcol/otelcontribcol /
ENTRYPOINT ["/otelcontribcol"]
CMD ["--config", "/etc/otel/config.yaml"]
