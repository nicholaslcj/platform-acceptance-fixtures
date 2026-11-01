# Platform acceptance fixtures

Two small, business-independent Helm Charts for validating a deployment platform:

| Chart | Structure | Intended public image |
| --- | --- | --- |
| `acceptance-http` | Deployment and ClusterIP Service | [traefik/whoami](https://github.com/traefik/whoami) |
| `acceptance-cron` | CronJob with an init container | [Docker Official Image BusyBox](https://github.com/docker-library/busybox) |

The HTTPS Helm repository is <https://nicholaslcj.github.io/platform-acceptance-fixtures>. Source is in `charts/`; reviewed, versioned packages and their index are in `docs/`.

```sh
helm repo add platform-acceptance https://nicholaslcj.github.io/platform-acceptance-fixtures
helm repo update platform-acceptance
helm show values platform-acceptance/acceptance-http --version 0.1.0
```

All default image references use `registry.example.invalid` and placeholder digests. Supply reviewed, immutable image references before deployment. Match image OS/CPU architecture to the intended cluster. Neither this repository nor its packages contain real environment configuration, credentials, business source, deployment permissions, or cluster access.

Both Charts use non-root containers, read-only root filesystems, no ServiceAccount token mounting, and namespaced resources. They have explicit values schemas and no Chart dependencies, hooks, CRDs, persistent data, or external service requirements. The different values layouts and resource structures intentionally exercise generic deployment contracts.

An HTTP deployment is accepted only after current workloads are ready and the Service answers `/health` and `/`. A CronJob is accepted only after a new Job from the selected revision completes, including its init container. Synchronization alone is not runtime acceptance. These fixtures do not validate application business behavior, databases, or production suitability.

See [RELEASING.md](RELEASING.md) for the manual, reviewed packaging workflow. Published versions must not be overwritten. SHA-256 checksums in `docs/SHA256SUMS` identify exact package bytes; retain reviewed digests independently in deployment inputs. Checksums alone are not a signature or authorization.
