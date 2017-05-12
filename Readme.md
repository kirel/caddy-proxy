# caddy-proxy

caddy-proxy automatically generates [Caddy](https://caddyserver.com/) reverse proxy configurations for docker containers like [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) for Nginx.

## Usage

The template generation only works with docker-compose file version 2.

First, you'll need to create an external docker network named 'caddy-proxy'.

```bash
$ docker network create -d bridge caddy-proxy
```

Then Clone this repository.

```bash
$ git clone https://github.com/kirel/caddy-proxy
$ cd caddy-proxy
```

Set a proper email address for `LETSENCRYPT_EMAIL` in [`caddy/docker-compose.yml`](caddy/docker-compose.yml).

And start the `caddy` and `caddy-gen` containers.

```bash
$ cd caddy
$ docker-compose up -d
```

Then set the `VIRTUAL_HOST` variable to a fully qualified domain name in your app (see `app1/docker-compose.yml` or `app2/docker-compose.yml` for examples).

```yaml
version: "2"

networks:
  proxy-tier:
    external:
      name: caddy-proxy

services:
  app1:
    container_name: app1
    image: httpd
    networks:
      - proxy-tier
    environment:
      VIRTUAL_HOST: "<YOUR FQDN>"
      VIRTUAL_NETWORK: "caddy-proxy"
      VIRTUAL_PORT: 80
    restart: always

```

Start your app:

```bash
$ cd ../yourapp
$ docker-compose up -d
```

Please note, that this `caddy` configuration uses the [Letsencrypt staging environment](https://letsencrypt.org/docs/staging-environment/). This means your browser will warn you about an insecure connection. For use in production remove the `-ca=https://acme-staging.api.letsencrypt.org/directory` flag in `caddy/docker-compose.yaml`.
