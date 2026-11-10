ARG APP_ROOT=/app

# skopeo list-tags docker://golang | jq -r '.Tags[]' | grep -E '^[0-9]+\.[0-9]+.[0-9]+-alpine' | sort -V
# skopeo inspect --raw docker://golang:1.27.2-alpine3.24 | sha256sum
FROM golang:1.27.4-alpine3.24@sha256:0a5f7fe796599ff487605987432e7619ae45beeb2fba4c2ce0b907879932aebf AS builder
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN apk --update --no-cache add ca-certificates
RUN go install go.opentelemetry.io/collector/cmd/builder@v0.165.0

COPY cmd/ ./cmd
RUN builder --config cmd/otelcontribcol/builder-config.yaml --verbose

# skopeo list-tags docker://alpine | jq -r '.Tags[]' | grep -E '^[0-9]+\.[0-9]+.[0-9]+' | sort -V
# skopeo inspect --raw docker://alpine:3.24.4 | sha256sum
FROM alpine:3.24.4@sha256:f59abe656b692d901e184be3445e084786602108599d2a51d229c0f5a48c71bf AS runner
ARG APP_ROOT
ARG USER_UID=10001
ARG USER_GID=10001

USER ${USER_UID}:${USER_GID}

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder ${APP_ROOT}/cmd/otelcontribcol/otelcontribcol /
ENTRYPOINT ["/otelcontribcol"]
CMD ["--config", "/etc/otel/config.yaml"]
