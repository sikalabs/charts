# Kycloak
A Helm chart for deploying Keycloak with PostgreSQL and Ingress support.

## Installation
```shell
helm repo add alf https://gitlab.pastyrik.dev/api/v4/projects/22/packages/helm/stable --username <username> --password <password>
helm install scheduled-app alf/keycloak --version 0.1.0
```

## Configuration
For the list of all available configuration options, see the [values.yaml](./values.yaml) file.
