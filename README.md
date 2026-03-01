# dochub-action

Reusable GitHub Actions workflows for [DocumentationHub](https://documentation-hub.com) and Docker deployments via [CoffeeTime](https://github.com/datastudy-nl/coffeetime).

## Workflows

### 📚 Generate Documentation

Automatically trigger DocumentationHub to generate and PR documentation for your repository.

#### Quick Start

1. Add your DocumentationHub API key as a repository secret named `DOCUMENTATIONHUB_API_KEY`
2. Create `.github/workflows/documentationhub.yml`:

```yaml
name: DocumentationHub - Generate Docs

on:
  push:
    branches: [main]
    branches-ignore:
      - 'documentationhub-run-*'
    paths-ignore:
      - '**/*.md'
      - 'docs/**'
  workflow_dispatch:

jobs:
  docs:
    uses: datastudy-nl/dochub-action/.github/workflows/generate-docs.yml@main
    secrets:
      api_key: ${{ secrets.DOCUMENTATIONHUB_API_KEY }}
```

#### Inputs

| Name       | Required | Default                          | Description                                    |
|------------|----------|----------------------------------|------------------------------------------------|
| `base_url` | No       | `https://documentation-hub.com`  | DocumentationHub instance URL                  |
| `auto_pr`  | No       | `true`                           | Automatically create a PR with generated docs  |

#### Secrets

| Name      | Required | Description                              |
|-----------|----------|------------------------------------------|
| `api_key` | Yes      | Your DocumentationHub API key (from dashboard) |

---

### ☕ Docker Deploy

Docker image builds & deployments via CoffeeTime.

#### Usage

```yaml
name: CI
on:
  push:
    branches: ['**']
  pull_request:
  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    uses: datastudy-nl/dochub-action/.github/workflows/docker-deploy.yml@main
    with:
      platforms: linux/amd64,linux/arm64
      tags: |
        ${{ github.ref_name }}
        ${{ startsWith(github.ref, 'refs/heads/main') && 'latest' || '' }}
      build_args: |
        GITHUB_ACTOR=${{ github.actor }}
    secrets: inherit
```

#### Inputs

| Name         | Required | Default                      | Description                                    |
|--------------|----------|------------------------------|------------------------------------------------|
| `platforms`  | No       | `linux/amd64,linux/arm64`    | Comma-separated list of target platforms        |
| `tags`       | No       | `""`                         | Newline-separated image tags to apply           |
| `build_args` | No       | `""`                         | Newline-separated Docker build arguments        |

All secrets are passed through via `secrets: inherit`.
