# Container publishing

## Fork testing

On the `test/docker-http` integration branch, pushes also trigger this workflow.
The image is published as
`ghcr.io/kquinsland/monarch-mcp-server:test-docker-http`, plus a commit tag.
This branch combines PRs #142 and #141 for testing before their upstream merge.
The push trigger is specific to this branch; `latest` is updated only by stable
releases.

## Upstream setup

The publishing workflow builds the root Dockerfile. Merge the Docker/HTTP work
in [PR #142](https://github.com/robcerda/monarch-mcp-server/pull/142) before merging
the automation in [PR #141](https://github.com/robcerda/monarch-mcp-server/pull/141).
See the [deployment README](../README.md#containerized-deployment) for image
configuration and authentication.

## Publish a release

Manually publish a GitHub release whose tag includes the Dockerfile and workflow.
`.github/workflows/ci_build-push-container.yaml` builds a Linux AMD64 image and
pushes it to `ghcr.io/<owner>/<repository>` for the repository running the workflow.
It uses the built-in `GITHUB_TOKEN` with `packages: write`; no separate registry
secret is required.

Images receive the release tag (for example, `v1.2.3`) and `sha-<short-commit>`.
Stable releases also update `latest`; prereleases do not. Draft releases do not
trigger a build until published.

## Build manually

Open **Actions → Build and Push Container → Run workflow** and select a branch.
To build a tag with the GitHub CLI:

```bash
gh workflow run ci_build-push-container.yaml --ref v1.2.3
```

The workflow must exist on the default branch for manual dispatch. The selected
ref must contain the Dockerfile and workflow. Manual runs publish the selected
branch/tag and commit tags without updating `latest`.
