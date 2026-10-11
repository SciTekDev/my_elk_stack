# My ELK Stack

A lightweight local ELK (Elasticsearch, Logstash, Kibana) + Filebeat lab stack, orchestrated with Docker Compose. Useful for experimenting with log ingestion and visualization.

## Components

- **Consul** (`1.19`) — service discovery agent, exposed on port `8500` (UI) and `8600/udp`. Joins the external `lab-network` Docker network so other local projects can reach it.
- **Elasticsearch** (`8.15.0`) — single-node instance, RAM capped at 512MB, exposed on port `9200`. Security (`xpack.security`) is disabled for local testing.
- **Kibana** (`8.15.0`) — web UI for exploring data, exposed on port `5601`.
- **Logstash** (`8.15.0`) — receives JSON log lines over TCP on port `5000` and forwards them to Elasticsearch, indexed as `logstash-YYYY.MM.dd`. Pipeline config: [logstash.conf](./logstash.conf).
- **Filebeat** (`8.15.0`) — tails Docker container logs (`/var/lib/docker/containers/*/*.log`), enriches events with Docker metadata, and ships them to Elasticsearch. Config: [filebeat.yml](./filebeat.yml).

## Prerequisites

- Docker and Docker Compose installed.

## Usage

Start the stack:

```powershell
docker compose up -d
```

Check that all services are running:

```powershell
docker compose ps
```

Access Kibana at [http://localhost:5601](http://localhost:5601).

Send a test log line to Logstash (JSON lines over TCP, port 5000):

```powershell
'{"message": "hello from test"}' | ncat localhost 5000
```

Stop the stack:

```powershell
docker compose down
```

To also remove stored Elasticsearch data:

```powershell
docker compose down -v
```

## Notes

- Data persists in the `es_lab_data` Docker volume between restarts (unless removed with `-v`).
- This setup is intended for local development/testing, not production use (security features are disabled).
