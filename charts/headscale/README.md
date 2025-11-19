# headscale Helm Chart

Headscale (open-source Tailscale control server) packaged with an optional Headplane UI. This chart is built for multi-tenant installs, with configurable domains, DB/OIDC, DERP, and ACL policy.

- Headscale provides the control-plane and can serve an embedded DERP relay for TCP-based relay when UDP is blocked.
- Headplane is an optional web UI to manage Headscale via API.

## Features
- Single or dual app deployment: enable only Headscale, only Headplane, or both.
- Embedded DERP behind your Ingress, with path-based routing (default `/derp`).
- ACL policy (HUJSON) via ConfigMap, hot-reload friendly.
- Pluggable DB and OIDC secrets (use existing or let chart create placeholders).
- PVCs for Headscale keys/config and Headplane data.

## Requirements
- Kubernetes 1.22+
- Ingress controller (Nginx tested) when `ingress.enabled=true`.
- cert-manager if using auto TLS with chart annotations.

## Quickstart

Install both Headscale and Headplane with defaults:

```sh
helm upgrade --install hs helm/headscale \
  --namespace headscale --create-namespace
```

Install Headscale only (no Headplane):

```sh
helm upgrade --install hs helm/headscale \
  --namespace headscale --create-namespace \
  --set headplane.enabled=false
```

Provide real secrets at install time (recommended):

```sh
# DB password from file
helm upgrade --install hs helm/headscale \
  -n headscale --create-namespace \
  --set headscale.db.existingSecret= \
  --set-file headscale.db.passwordKey=./db-password.txt
```

Or reference existing secrets:

```sh
helm upgrade --install hs helm/headscale \
  -n headscale --create-namespace \
  --set headscale.db.existingSecret=my-db-secret \
  --set headscale.oidc.existingSecret=my-oidc-secret
```

## How DERP is wired
- Embedded DERP is enabled by default and advertised on port 443.
- Traffic reaches Headscale via your Ingress at `https://<host>/derp` and is proxied to the Headscale HTTP port (default 8080). TLS terminates at the Ingress.
- UDP/STUN is required by Headscale v0.27 when DERP is enabled; the chart binds STUN to `127.0.0.1:3478` so it’s not externally exposed.

If you hit issues with DERP via Ingress, consider:
- Switching to a dedicated LoadBalancer on 443 and adjusting `derp.derpPort` accordingly.
- Using an Ingress controller with HTTP/2 support and long-lived connection timeouts (the chart sets long read/send timeouts by default).

## Values reference

Top-level toggles:
- `headscale.enabled`: Deploy Headscale (default true)
- `headplane.enabled`: Deploy Headplane (default true)

Headscale:
- `headscale.image.repository` (default `ghcr.io/juanfont/headscale`)
- `headscale.image.tag` (default `v0.27.1`)
- `headscale.serverUrl`: Public URL used in links and DERP advertisement
- `headscale.prefixes.v4`, `headscale.prefixes.v6`: IP ranges to allocate
- `headscale.dns.baseDomain`, `headscale.dns.magicDNS`, `headscale.dns.nameserversGlobal`
- `headscale.db.*`: host, name, user, port, ssl, secret ref
- `headscale.oidc.enabled`, `issuer`, `clientId`, `existingSecret`, `clientSecretKey`
- `headscale.derp.*`: `enabled`, `urls`, `derpPort`, `stunListenAddr`, `regionId/code/name`, `privateKeyPath`
- `headscale.policy.create`, `headscale.policy.hujson`: HUJSON ACL policy
- `headscale.persistence.*`: PVC settings for `/etc/headscale`
- `headscale.ingress.*`: className, annotations, `host`, TLS secret, `derpPath`

Headplane:
- `headplane.image.repository`, `tag`
- `headplane.config.server.cookieSecret`: must be exactly 32 chars
- `headplane.config.headscale.internalUrl`: in-cluster URL to Headscale (default points to this chart’s service)
- `headplane.config.headscale.publicUrl`: public URL (defaults to `headscale.serverUrl`)
- `headplane.persistence.*`, `headplane.ingress.*`

## Example: Headscale only, custom domains and DB

```yaml
headscale:
  enabled: true
  serverUrl: https://vpn.acme.example
  ingress:
    host: vpn.acme.example
    tls:
      secretName: vpn-acme-example-tls
  db:
    host: postgres-rw.postgres.svc.cluster.local
    name: headscale
    user: headscale
    existingSecret: acme-headscale-db
    passwordKey: HEADSCALE_DATABASE_POSTGRES_PASS
  derp:
    enabled: true
    urls: ""            # only embedded DERP
    derpPort: 443       # advertised to clients
    stunListenAddr: 127.0.0.1:3478
  policy:
    create: true
    hujson: |-
      {
        "groups": {},
        "hosts": {},
        "acls": [
          { "action": "accept", "src": ["*"], "dst": ["*:*"] }
        ]
      }
headplane:
  enabled: false
```

## Upgrading existing installs
- This chart does not set explicit `namespace:`; set the namespace via `--namespace` and keep it consistent.
- Changing `ingress.host` or `tls.secretName` requires DNS and cert-manager readiness.
- If you switch from chart-created secrets to external secrets, set `existingSecret` and remove the chart-managed secret.

## Troubleshooting
- Pod fails with DERP errors
  - Ensure `headscale.derp.enabled=true` and `headscale.derp.stunListenAddr` is non-empty.
  - If DERPMap is empty, set `headscale.derp.urls` to Tailscale default temporarily:
    `https://controlplane.tailscale.com/derpmap/default`.
- TLS warning in logs
  - Expected when TLS terminates at Ingress but `serverUrl` is https.
- ACL policy reloads
  - Headscale reads `policy.path` (`HEADSCALE_POLICY_PATH`) and will reload on change in recent versions; if not, restart the pod.

## License
This chart contains configuration files from this repository and does not add a license header by default.

