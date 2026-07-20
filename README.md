# opentelemetry-collector-contrib
Custom Otel Collector with removed/added components, stability is questionable

Contains only modified https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/cmd/otelcontribcol

Check the OtelCol [releases](https://github.com/open-telemetry/opentelemetry-collector-releases/releases) and bump component versions when needed

## `cmd/otelcontribcol-obi`

`cmd/otelcontribcol-obi` is the Otel Collector with OBI receiver installed - [build docs](https://opentelemetry.io/docs/zero-code/obi/configure/collector-receiver/#configuration).
This collector should be used as a daemonset only.

`obi-generator` build is inspired by original OBI build step - [ref](https://github.com/open-telemetry/opentelemetry-ebpf-instrumentation/blob/main/Dockerfile)

## Notes

`.github/workflows/release.yaml` would benefit from the GHA reusable workflow.
