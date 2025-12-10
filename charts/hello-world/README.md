[SikaLabs (sikalabs.com)](https://sikalabs.com) | [Ondrej Sika (sika.io)](https://sika.io) | [__Skoleni Kubernetes__](https://ondrej-sika.cz/skoleni/kubernetes/) 🚀💻

# sikalabs/hello-world

A simple Helm chart for deploying a hello-world-server to Kubernetes.

## Configuration

See the [values.yaml](./values.yaml) for configurable parameters.

- https://github.com/sikalabs/charts/blob/master/charts/hello-world/values.yaml (values.yaml on GitHub)
- https://artifacthub.io/packages/helm/sikalabs/hello-world?modal=values (values.yaml on Artifact Hub)

## Installation

```bash
helm upgrade --install \
  my-hello-world hello-world \
  --repo https://https://helm.sikalabs.io
```
