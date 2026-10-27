# opentelemetry-collector-contrib
Custom Otel Collector with removed/added components, stability is questionable

Contains only modified https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/cmd/otelcontribcol

Check the OtelCol [releases](https://github.com/open-telemetry/opentelemetry-collector-releases/releases) and bump component versions when needed

## Notes

- `.github/workflows/release.yaml` would benefit from the GHA reusable workflow.
- CLI list tags hint

  ```sh
  skopeo list-tags docker://ghcr.io/maksym-iv/opentelemetry-collector-contrib-build | jq -r '.Tags[]' | grep -E '^[0-9]+\.[0-9]+.[0-9]+' | sort -V
  ```
