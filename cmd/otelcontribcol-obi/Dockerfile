ARG APP_ROOT=/app

# skopeo list-tags docker://ghcr.io/open-telemetry/obi-generator | jq '.Tags' | sort -V
# skopeo inspect --raw docker://ghcr.io/open-telemetry/obi-generator:0.2.15 | sha256sum
FROM ghcr.io/open-telemetry/obi-generator:0.2.17@sha256:1ae9406f2566e32ccf5d8ba8e5ff0586a322300ae82870988ad605429021174a AS ebpf-generator
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN git clone --branch v0.10.0 https://github.com/open-telemetry/opentelemetry-ebpf-instrumentation.git ./
RUN /generate.sh

FROM golang:1.27.3-alpine3.24@sha256:b8c9b82affbbb82a020bbcf998dad2ba9516944ac7e883524642a481a9505e9c AS builder
ARG APP_ROOT
WORKDIR ${APP_ROOT}

RUN apk --update --no-cache add ca-certificates git
RUN go install go.opentelemetry.io/collector/cmd/builder@v0.163.0
COPY --from=ebpf-generator ${APP_ROOT} ${APP_ROOT}/opentelemetry-ebpf-instrumentation


COPY cmd/ ./cmd
RUN builder --config cmd/otelcontribcol-obi/builder-config.yaml --verbose

FROM scratch AS runner
ARG APP_ROOT

COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder ${APP_ROOT}/cmd/otelcontribcol/otelcontribcol /
ENTRYPOINT ["/otelcontribcol"]
CMD ["--config", "/etc/otel/config.yaml"]
