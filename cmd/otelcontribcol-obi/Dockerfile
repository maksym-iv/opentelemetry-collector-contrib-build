ARG APP_ROOT=/app

# skopeo list-tags docker://ghcr.io/open-telemetry/obi-generator | jq '.Tags' | sort -V
# skopeo inspect --raw docker://ghcr.io/open-telemetry/obi-generator:0.2.15 | sha256sum
FROM ghcr.io/open-telemetry/obi-generator:0.2.16@sha256:3a8959e5253f2445b782b4f720ed54f6396fce350082486442e0b71ac02ff106 AS ebpf-generator
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN git clone --branch v0.10.0 https://github.com/open-telemetry/opentelemetry-ebpf-instrumentation.git ./
RUN /generate.sh

FROM golang:1.27.1-alpine3.24@sha256:4cb7ac979db5fcc41cae44b2227ba5ab8a51e8807f40d9ba4dee20a0ad960b5b AS builder
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN apk --update --no-cache add ca-certificates git
RUN go install go.opentelemetry.io/collector/cmd/builder@v0.161.0
COPY --from=ebpf-generator ${APP_ROOT} ${APP_ROOT}/opentelemetry-ebpf-instrumentation


COPY cmd/ ./cmd
RUN builder --config cmd/otelcontribcol-obi/builder-config.yaml --verbose

FROM scratch AS runner
ARG APP_ROOT

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder ${APP_ROOT}/cmd/otelcontribcol/otelcontribcol /
ENTRYPOINT ["/otelcontribcol"]
CMD ["--config", "/etc/otel/config.yaml"]
