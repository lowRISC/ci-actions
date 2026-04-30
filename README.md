# ci-actions

Reusable GitHub Actions for lowRISC CI workflows.

## Actions

### `ca-github-token`

Obtains a short-lived GitHub installation access token from the lowRISC CA, using GitHub's OIDC provider to authenticate the request.
The token is scoped according to the named role granted to the calling workflow.

**Requires `id-token: write` in the calling job.**

#### Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `audience` | Intended audience for the GitHub OIDC JWT | `https://ca.lowrisc.org` |
| `org` | GitHub organisation name to obtain a token for | `lowRISC` |
| `role` | IAM role to request from the CA. **Required.** | — |
| `repo` | Repository in `org/repo` format; passed to the CA for use in CEL conditions on bindings. Does not restrict the token scope. | `""` |

#### Outputs

| Output | Description |
|--------|-------------|
| `token` | Short-lived GitHub installation access token |

#### Example

```yaml
jobs:
  build:
    permissions:
      id-token: write
    steps:
      - name: Get a lowRISC CA token
        id: get-token
        uses: lowrisc/ci-actions/ca-github-token@v2
        with:
          role: ci-writer

      - name: Use token
        run: |
          gh auth login --with-token <<< "${{ steps.get-token.outputs.token }}"
```

To pass a repository for use in CEL binding conditions:

```yaml
      - uses: lowrisc/ci-actions/ca-github-token@v2
        with:
          role: ci-writer
          repo: "${{ github.repository }}"
```

---

### `ca-request`

Makes an authenticated request to any lowRISC CA endpoint, using GitHub's OIDC provider to authenticate.
Use this for CA endpoints other than the GitHub installation access token endpoint — for example, Nix cache token endpoints.

**Requires `id-token: write` in the calling job.**

#### Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `audience` | Intended audience for the GitHub OIDC JWT | `https://ca.lowrisc.org` |
| `ca_api_endpoint` | CA endpoint URL. **Required.** | — |
| `ca_api_method` | HTTP method | `GET` |
| `body` | Optional JSON request body. Sent with `Content-Type: application/json` when non-empty. | `""` |

#### Outputs

| Output | Description |
|--------|-------------|
| `token` | Token returned by the CA endpoint |

#### Examples

```yaml
      - name: Get token for writing to the public Nix cache
        id: public-cache-token
        uses: lowrisc/ci-actions/ca-request@v2
        with:
          ca_api_endpoint: "https://ca.lowrisc.org/api/nix-caches/public/token"
```

## License

Copyright lowRISC contributors.
SPDX-License-Identifier: MIT
