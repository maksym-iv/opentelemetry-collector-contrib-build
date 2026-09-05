ARG APP_ROOT=/app

# skopeo list-tags docker://ghcr.io/open-telemetry/obi-generator | jq '.Tags' | sort -V
# skopeo inspect --raw docker://ghcr.io/open-telemetry/obi-generator:0.2.15 | sha256sum
FROM ghcr.io/open-telemetry/obi-generator:0.2.15@sha256:9cbb1b567377d5779b04e6bcdb87431c77a19e797b4630eba30f5417de96ea33 AS ebpf-generator
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN git clone --branch v0.10.0 https://github.com/open-telemetry/opentelemetry-ebpf-instrumentation.git ./
RUN /generate.sh

FROM golang:1.27.1-alpine3.24@sha256:cf6fca6641884b8433441b2b0652976f975e1d0fdd26d177eaaf8596087f3125 AS builder
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN apk --update --no-cache add ca-certificates
RUN go install go.opentelemetry.io/collector/cmd/builder@v0.159.0
COPY --from=ebpf-generator ${APP_ROOT} ${APP_ROOT}/opentelemetry-ebpf-instrumentation


COPY cmd/ ./cmd
RUN builder --config cmd/otelcontribcol-obi/builder-config.yaml --verbose

FROM scratch AS runner
ARG APP_ROOT

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder ${APP_ROOT}/cmd/otelcontribcol/otelcontribcol /
ENTRYPOINT ["/otelcontribcol"]
CMD ["--config", "/etc/otel/config.yaml"]
