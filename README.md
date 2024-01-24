# Helmcode Yopass - Share Secrets Securely

Yopass is a project for sharing secrets in a quick and secure manner\*.
The sole purpose of Yopass is to minimize the amount of passwords floating around in ticket management systems, Slack messages and emails. The message is encrypted/decrypted locally in the browser and then sent to yopass without the decryption key which is only visible once during encryption, yopass then returns a one-time URL with specified expiry da

## Installation / Configuration

Here are the server configuration options.

Command line flags:

```console
$ yopass-server -h
      --address string     listen address (default 0.0.0.0)
      --database string    database backend ('memcached' or 'redis') (default "memcached")
      --max-length int     max length of encrypted secret (default 10000)
      --memcached string   Memcached address (default "localhost:11211")
      --metrics-port int   metrics server listen port (default -1)
      --port int           listen port (default 1337)
      --redis string       Redis URL (default "redis://localhost:6379/0")
      --tls-cert string    path to TLS certificate
      --tls-key string     path to TLS key
```

Encrypted secrets can be stored either in Memcached or Redis by changing the `--database` flag.

### Docker Compose

```console
docker-compose up -d
```
Yopass will then be available under the domain you specified through `VIRTUAL_HOST` / `LETSENCRYPT_HOST`.

Advanced users that already have a reverse proxy handling TLS connections can use the `insecure` setup:

```console
cd deploy/docker/compose/insecure
docker-compose up -d
```
Afterwards point your reverse proxy to `127.0.0.1:3003`.

### Docker

With TLS encryption

```console
docker run --name memcached_yopass -d memcached
docker run -p 443:1337 -v /local/certs/:/certs \
    --link memcached_yopass:memcached -d jhaals/yopass --memcached=memcached:11211 --tls-key=/certs/tls.key --tls-cert=/certs/tls.crt
```
Afterwards yopass will be available on port 443 through all IP addresses of the host, including public ones. If you want to limit the availability to a specific IP address use `-p` like so: `-p 127.0.0.1:443:1337`.

Without TLS encryption (needs a reverse proxy for transport encryption):

```console
docker run --name memcached_yopass -d memcached
docker run -p 127.0.0.1:80:1337 --link memcached_yopass:memcached -d jhaals/yopass --memcached=memcached:11211
```

Afterwards point your reverse proxy that handles the TLS connections to `127.0.0.1:3003`.

### Kubernetes

```console
kubectl apply -f deploy/yopass-k8.yaml
kubectl port-forward service/yopass 1337:1337
```

_This is meant to get you started, please configure TLS when running yopass for real._

## Monitoring

Yopass optionally provides metrics in the [OpenMetrics][] / [Prometheus][] text
format. Use flag `--metrics-port <port>` to let Yopass start a second HTTP
server on that port making the metrics available on path `/metrics`.

Supported metrics:

- Basic [process metrics][] with prefix `process_` (e.g. CPU, memory, and file descriptor usage)
- Go runtime metrics with prefix `go_` (e.g. Go memory usage, garbage collection statistics, etc.)
- HTTP request metrics with prefix `yopass_http_` (HTTP request counter, and HTTP request latency histogram)

[openmetrics]: https://openmetrics.io/
[prometheus]: https://prometheus.io/
[process metrics]: https://prometheus.io/docs/instrumenting/writing_clientlibs/#process-metrics

## Translations

Yopass has third party support for other languages. That means you can write translations for the language you'd like or use a third party language file. Please note that yopass itself is english only and any other translations are community supported.

Here's a list of available translations:
- [German](https://github.com/Anturix/yopass-german)
- [French](https://github.com/NicolasStr/yopass-french)
- [Spanish](https://github.com/nbensa/yopass-spanish)
- [Polish](https://github.com/mdurajewski/yopass-polish)
- [Dutch](https://github.com/KevinRosendaal/yopass-dutch)