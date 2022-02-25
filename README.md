# Internet Monitoring Docker Stack with Prometheus + Grafana
## Pre-requisites

Make sure Docker and [Docker Compose](https://docs.docker.com/compose/install/) are installed on your Docker host machine.

With this branch you need a way of exposing grafana. If you just want it to work switch to the [main](https://github.com/tb942/internet-monitoring) branch. Otherwise, check out my [nginx config](https://github.com/tb942/nginx-https-reverse-proxy).

## Quick Start

### Pull the repo

```bash
git clone https://github.com/tb942/internet-monitoring
cd internet-monitoring
```

### Create a `/grafana/config.monitoring` file

```bash
touch ./grafana/config.monitoring
```

Use this file to set your Grafana config environment variables. you can find a list of options here: [Grafana Configuration docs](https://grafana.com/docs/grafana/latest/administration/configuration/)

An example file would be:
```.env
GF_SECURITY_ADMIN_PASSWORD=<your login password>
GF_USERS_ALLOW_SIGN_UP=false
```

### Create the `main` docker network (if it doesn't exist)
```bash
docker network create main
```

### Stand the stack up
```bash
docker-compose up --detach
```

Go to [http://localhost:3000/d/o9mIe_Aik/internet-connection](http://localhost:3030/d/o9mIe_Aik/internet-connection) (change `localhost` to your docker host ip/name).

## Configuration

To change what hosts you ping you change the `targets` section in [/prometheus/pinghosts.yaml](./prometheus/pinghosts.yaml) file.

For speedtest the only relevant configuration is how often you want the check to happen. *It is at 10 minutes by default which might be too much if you have limit on downloads*. This is changed by editing `scrape_interval` under `speedtest` in [/prometheus/prometheus.yml](./prometheus/prometheus.yml).

Once configurations are done, run the following command:
```bash
docker-compose up --detach
```

That's it. docker-compose builds the entire Grafana and Prometheus stack automagically.

The grafana instance is not yet externally accessible, and instead exists within the external docker network `main`. This is ideal for exposing through a reverse proxy such as nginx to provide the service on a privelleged HTTP/HTTPS port with a domain. To connect an nginx container simply put it on the same `main` external docker network and set the proxy_pass value to `http://grafana:3000`.

username - `admin`

password - `<your login password>` (Password you set in the `/grafana/config.monitoring` env file)

The DataSource and Dashboard for Grafana are automatically provisioned.

## Updating
```bash
docker-compose pull
docker-compose up --detach
```
