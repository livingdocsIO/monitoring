# A simple Loki setup with Grafana

This setup uses Loki for logs, Prometheus for metrics, and Jaeger for tracing. It will automatically collect logs from other Docker containers running on the same server if the installation steps below are followed.

The Livingdocs server can be configured to send metrics data to the stack, and there is also a way to send log data when running the service without the use of Docker (for local development purposes). More details can be found in the [Livingdocs documentation](https://docs.livingdocs.io/).

The stack includes the following:

* **cAdvisor** [[Docs](https://github.com/google/cadvisor)] [[Local UI](http://localhost:9081/)] - Provides resource usage and performance metrics of Docker containers to Prometheus
* **Grafana** [[Docs](https://grafana.com/docs/grafana/latest/)] [[Local UI](http://localhost:3000/)] - UI to explore logs and metrics using queries, charts, and alerts
* **Jaeger** [[Docs](https://www.jaegertracing.io/docs/)] [[Local UI](http://localhost:16686/)] - Provides tracing data which is linked to each incoming Livingdocs server request
* **Loki** [[Docs](https://grafana.com/docs/loki/latest/)] - Ingests logs which can be viewed and queried from within Grafana
* **OpenTelemetry Collector** [[Docs](https://opentelemetry.io/docs/collector/)] - Collects metrics data from Livingdocs server and exports the data to Prometheus
* **Prometheus** [[Docs](https://prometheus.io/docs/)] [[Local UI](http://localhost:3001/)] - A monitoring toolkit for timeseries based metrics
* **Vector** [[Docs](https://vector.dev/docs/)] - Transforms Docker logs and send them to Loki, as well as collecting logs from local Node.js processes if required

## Installation

- Clone the repository and launch cAdvisor, Grafana, Jaeger, Loki, OpenTelemetry Collector, Prometheus, and Vector:
  ```sh
  git clone git@github.com:livingdocsIO/monitoring.git
  cd monitoring
  docker-compose up -d
  ```
- Navigate to <http://localhost:3000/> and log in using the default Grafana user (admin/admin).

## Troubleshooting

### Directory permissions

If you see any errors about being unable to create files or directories when starting the containers then the issue is probably related to user permissions. We have chosen to run the containers which write to disk with uid 1000 and gid 1000. We see this as being a sensible default for most users, but if it causes problems you can either change the `user: "1000:1000"` lines in docker-compose.yaml to values which suit your environment, or remove the lines and set the default container permissions on the directories:
```sh
chown -R 472:472 ./data/grafana
chown -R 0:0 ./data/jaeger
chown -R 10001:10001 ./data/loki
chown -R 65534:65534 ./data/prometheus
```

### Jaeger EMSGSIZE error

Jaeger needs higher UDP package sizes than the maximum configured on Mac. The default value should be increased using:
```sh
sudo sysctl net.inet.udp.maxdgram=65536
```
