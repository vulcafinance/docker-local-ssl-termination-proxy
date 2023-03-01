# Docker Local SSL Termination Proxy

[![Docker Pulls](https://img.shields.io/docker/pulls/esplo/docker-local-ssl-termination-proxy.svg?style=for-the-badge)](https://hub.docker.com/r/esplo/docker-local-ssl-termination-proxy/)

A simple SSL Termination Proxy for accessing https://localhost.internal.

This proxy is easy to use (1 command to launch) and nothing is installed on your local machine thanks to Docker.

## Requirement

- Docker (20.10.0+)

## Usage

Assume that your application is running on `http://localhost.internal:8000`, the following command makes us accept requests to `https://localhost.internal`.

### Mac OSX

```bash
docker run -it \
  -e "PORT=8000" \
  -p 443:443 \
  --rm \
  esplo/docker-local-ssl-termination-proxy
```

### Linux

```bash
docker run -it \
  --add-host=host.docker.internal:host-gateway
  -e "PORT=8000" \
  -p 443:443 \
  --rm \
  esplo/docker-local-ssl-termination-proxy
```

### Other platform

You need to make sure that `host.docker.internal` is resolved properly inside docker containers. For majority of platforms that don't resolve it automatically (like Docker Desktop for Mac), setting `--add-host=host.docker.internal:host-gateway` is enough.

### Test connection

```bash
$ curl -k https://localhost.internal/
```

### Locally-trusted development certificates

The container can be configured to use custom certificates with the `SSL_CERT` and `SSL_KEY` environment variables. You can use [mkcert](https://github.com/FiloSottile/mkcert) to generate locally-trusted development certificates:

```bash
$ mkdir -p ssl
$ mkcert -install
$ mkcert --cert-file ssl/localhost.internal.pem --key-file ssl/localhost.internal.key localhost.internal 127.0.0.1 ::1
```

and mount them to the container using [bind mounts](https://docs.docker.com/storage/bind-mounts/):

```bash
$ docker run -it \
  -e 'PORT=8000' \
  -e 'SSL_CERT=localhost.internal.pem' \
  -e 'SSL_KEY=localhost.internal.key' \
  -p 443:443 \
  -v "$(pwd)"/ssl:/etc/nginx/ssl/ \
  --rm \
  esplo/docker-local-ssl-termination-proxy
```

## Troubleshoot

### "Your connection is not private" in Chrome

Click "Advanced" button, then click "Proceed anyway".

## License

See the [LICENSE](LICENSE) file for license rights and limitations (MIT).
