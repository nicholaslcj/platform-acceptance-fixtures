# Releasing a fixture Chart

The initial packages were prepared with Helm 4.2.4. This repository does not trigger a cluster deployment or require GitHub Actions credentials.

1. Review changes to Chart source, defaults, schemas, and templates. Keep all real targets, credentials, and environment values outside this repository.
2. Increment the changed Chart's `version`. Refuse publication if that name/version already exists in `docs/index.yaml` or its package is already present.
3. Run strict Helm lint and render with independently reviewed values. Verify install and upgrade behavior, all images including init containers, and supported architectures. Placeholder defaults are useful for static checks only.
4. Package into a new temporary directory. Inspect the full archive: expected regular files only; no links, traversal, hidden extras, or secrets. Helm may normalize `Chart.yaml`; deployment snapshots must use the final package bytes.
5. Generate a merged index that retains all prior versions. Copy only the new reviewed package(s) and resulting index into `docs/`, and update checksums for all packages.
6. Review and commit the source, new package(s), index, and checksums together. Push a normal fast-forward commit. Do not replace historical packages or force-push published history.
7. GitHub Pages publishes `main:/docs`. After the Pages build succeeds, fetch the public HTTPS index and packages and compare their SHA-256 digests with the reviewed local files. Do not claim publication based only on a successful push.

Example packaging commands (substitute the reviewed Chart name and version):

```sh
package_dir="$(mktemp -d)"
helm lint charts/acceptance-http --strict
helm package charts/acceptance-http --destination "$package_dir"
helm repo index "$package_dir" \
  --url https://nicholaslcj.github.io/platform-acceptance-fixtures \
  --merge docs/index.yaml
```

Only copy the reviewed new version and merged index after checking that existing package bytes are unchanged. Preserve old packages for configuration rollback. The GitHub repository and Pages are public; never add internal infrastructure information or private deployment configuration.
