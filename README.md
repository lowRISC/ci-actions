# ci-actions

Reusable GitHub Actions for lowRISC CI workflows.

## Actions

### `lowrisc_ci_app_get_token`

Obtains a short-lived lowrisc-ci GitHub App installation access token from the lowRISC CA, using GitHub's OIDC provider to authenticate the request.

**Requires `id-token: write` in the calling job.**

#### Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `audience` | Intended audience for the GitHub OIDC JWT | `https://ca.lowrisc.org` |
| `ca_api_endpoint` | lowRISC CA endpoint from which to obtain a token | `https://ca.lowrisc.org/api/github/repos/<repo>/token` |
| `ca_api_method` | HTTP method to use when requesting a token from the CA endpoint | `POST` |

#### Outputs

| Output | Description |
|--------|-------------|
| `token` | Short-lived installation access token |

#### Example

```yaml
jobs:
  build:
    permissions:
      id-token: write
    steps:
      - name: Get a short-lived lowrisc-ci app token
        id: get-token
        uses: lowrisc/ci-actions/lowrisc_ci_app_get_token@v1

      - name: Use token
        run: |
          some-tool login https://example.lowrisc.org/ ${{ steps.get-token.outputs.token }}
```

To override the CA endpoint or HTTP method:

```yaml
      - uses: lowrisc/ci-actions/lowrisc_ci_app_get_token@v1
        with:
          ca_api_endpoint: "https://ca.lowrisc.org/api/nix-caches/public/token"
          ca_api_method: "GET"
```

## License

Copyright lowRISC contributors.
SPDX-License-Identifier: MIT
