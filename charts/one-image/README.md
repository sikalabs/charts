# one image - helm chart

    2019 Ondrej Sika <ondrej@ondrejsika.com>
    https://github.com/ondrejsika/one-image-helm

## Usage

Add repo

```
helm repo add ondrejsika https://helm.oxs.cz
```

Install

```
helm upgrade --install \
  <name> ondrejsika/one-image \
  --set image=<image> \
  --set host=<domain>
```

## Parameters

- `image` (default `ondrejsika/go-hello-world:2`) - Docker image you want to serve
- `host` (default `hello-world.local`) - Ingress hostname
- `www_redirect` (default `true`) - www alias with redirect
- `changeCause` (default `null`)
- `replicas` (default `1`)
- `dockerRegistry` (default `null`) - Private registry URL, eg. `registry.gitlab.com`
- `dockerRegistryAuth` (default `null`) - Base64 encoded `user:token`, eg. `dXNlcjp0b2tlbgo=`
- `restartAfterRedeploy` (default `false`) - restart pod after every update
- `containerEnv` (default `null`) - add environment variable to container

#### BasicAuth
You can define BasicAuth credentials in values.yaml
```
basicAuth:
  username: null (default)
  password: null (default)
```

#### LivenessProbe
You can define livenessProbe path and port in values.yaml
```
livenessProbe:
  path: / (default)
  port: http (default)
```
