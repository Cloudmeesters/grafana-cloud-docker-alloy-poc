# Grafana Cloud - Docker & Alloy - PoC

This is a simple proof-of-concept that illustrates the basics of collecting Docker metrics and writing them to Grafana Cloud using:

1. [Grafana Alloy](https://github.com/grafana/alloy)
1. [cAdvisor](https://github.com/google/cadvisor)
1. [node-exporter](https://github.com/prometheus/node_exporter)

## Prerequisites

As we'll be making use of Grafana Cloud's Free Tier, you need to have your account and stack ready. If you didn't already do so, you can sign-up [here](https://grafana.com/auth/sign-up?refCode=gr8nXVegJYnQ4VD).

Next, head over to your stack in Grafana Cloud and select "Send Metrics" under Prometheus. If no API token has been created yet, press "Generate now".
When done, copy `config.alloy.example` to `config.alloy` and merge the configuration snippet in there.

In order to collect metrics from Docker, you need to specify a `metrics-address` in the `daemon.json` configuration file. This daemon expects the file to be located at one of the following locations by default. If the file doesn't exist, create it.

* Linux: `/etc/docker/daemon.json`
* Windows Server: `C:\ProgramData\docker\config\daemon.json`
* Docker Desktop: Open the Docker Desktop settings and select Docker Engine to edit the file.

Add the following configuration to listen on the `docker0` interface:

```json
{
  "metrics-addr": "172.17.0.1:9323"
}
```

You can check which address is in use on your `docker0` interface using command (`172.17.0.1` in our case):

```bash
ip addr show docker0 | grep -Po 'inet \K[\d.]+'`
```

## Running

When all prerequisites have been met, this PoC can be launched using "Docker Compose":

```bash
docker compose up -d
```

Within a minute or so you should see your first metrics appear in Grafana Cloud.
To see what's happening, tail the logs using:

```bash
docker compose logs -f
```
