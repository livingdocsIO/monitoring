# A simple loki setup with grafana

## Installation


- Install the loki logging driver*
  ```
  docker plugin install grafana/loki-docker-driver  --alias loki --grant-all-permissions
  ```
- Configure the docker daemon to use the loki logging driver*
  ```
  {
    "experimental" : false,
    "debug" : true,
    "log-driver" : "loki",
    "log-opts" : {
      "loki-external-labels" : "container_name={{.Name}},container_id={{.ID}}",
      "loki-url" : "http://host.docker.internal:3100/api/prom/push",
      "loki-batch-size" : "400"
    }
  }
  ```
- Launch loki, grafana and promtail*
  ```
  docker-compose up -d
  ```


- Navigate to http://localhost:3000/explore and log in using the default grafana user (admin/admin)
