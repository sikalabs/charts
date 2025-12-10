[SikaLabs (sikalabs.com)](https://sikalabs.com) | [Ondrej Sika (sika.io)](https://sika.io) | [__Skoleni Kubernetes__](https://ondrej-sika.cz/skoleni/kubernetes/) 🚀💻

# sikalabs/one-image

A simple Helm chart for deploying a single image to Kubernetes.

## Configuration

See the [values.yaml](./values.yaml) for configurable parameters.

- https://github.com/sikalabs/charts/blob/master/charts/one-image/values.yaml (values.yaml on GitHub)
- https://artifacthub.io/packages/helm/sikalabs/one-image?modal=values (values.yaml on Artifact Hub)

## Installation

You have to provide required values `image` and `host`

```bash
helm upgrade --install \
  iceland-3 one-image \
  --repo https://https://helm.sikalabs.io \
  --set image=ghcr.io/ondrejsika/iceland-3 \
  --set host=iceland-3.k8s.sikademo.com
```
