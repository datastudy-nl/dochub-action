# dochub-action

Reusable GitHub Actions workflow for Docker image builds & deployments via [CoffeeTime](https://github.com/datastudy-nl/coffeetime).

## Usage

Add this to your repository's `.github/workflows/ci.yaml`:

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

## Inputs

| Name         | Required | Default                      | Description                                    |
|--------------|----------|------------------------------|------------------------------------------------|
| `platforms`  | No       | `linux/amd64,linux/arm64`    | Comma-separated list of target platforms        |
| `tags`       | No       | `""`                         | Newline-separated image tags to apply           |
| `build_args` | No       | `""`                         | Newline-separated Docker build arguments        |

All secrets are passed through via `secrets: inherit`.
