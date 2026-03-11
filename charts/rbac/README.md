# rbac

Helm chart for creating RBAC objects (Roles, ClusterRoles, RoleBindings, ClusterRoleBindings).

## Values

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Unique name for generated resources |
| `group` | string | Kubernetes Group subject name |
| `clusterWide` | bool | If true, creates ClusterRole/ClusterRoleBinding (default: false) |
| `rules` | list | RBAC policy rules. Required unless `existingClusterRole` or `existingRole` is set |
| `namespaces` | list of string | Namespaces for namespace-scoped resources. Required when not `clusterWide` |
| `existingClusterRole` | string | Bind to an existing ClusterRole instead of creating one |
| `existingRole` | string | Bind to an existing Role instead of creating one |

## Usage

```yaml
rbacRules:
  # Custom ClusterRole (cluster-wide)
  - name: admin-role
    group: kubernetes-admin
    clusterWide: true
    rules:
      - apiGroups: ["*"]
        resources: ["*"]
        verbs: ["*"]

  # Custom ClusterRole bound in specific namespaces
  - name: ch-devs-extensions
    group: "k8s:devs"
    clusterWide: true
    namespaces: [ch-kong, ch-camunda-orch]
    rules:
      - apiGroups: ["gateway.networking.k8s.io"]
        resources: ["httproutes"]
        verbs: ["*"]

  # Custom Role in multiple namespaces
  - name: pod-reader
    group: kubernetes-dev
    namespaces: [default, monitoring]
    rules:
      - apiGroups: [""]
        resources: [pods]
        verbs: [get, list, watch]

  # Bind to existing ClusterRole cluster-wide
  - name: view-all-binding
    group: kubernetes-viewer
    existingClusterRole: view
    clusterWide: true

  # Bind to existing ClusterRole in specific namespaces
  - name: dev-edit-binding
    group: kubernetes-dev
    existingClusterRole: edit
    namespaces: [default, staging]

  # Bind to existing Role in specific namespaces
  - name: dev-custom-binding
    group: kubernetes-dev
    existingRole: my-custom-role
    namespaces: [default]
```
