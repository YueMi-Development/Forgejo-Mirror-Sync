# Forgejo Mirror Sync

A lightweight GitHub Action that parses a Forgejo mirror repository URL and triggers a mirror sync via the Forgejo API.

## Usage

Add this action to any workflow that needs to force a Forgejo mirror update:

```yaml
jobs:
  mirror-sync:
    runs-on: ubuntu-latest
    steps:
      - uses: YueMi-Development/Forgejo-Mirror-Sync@v1
        with:
          repo_url: ${{ secrets.MIRROR_REPO_URL }}
          token: ${{ secrets.FORGEJO_TOKEN }}
```

## Inputs

| Name      | Description                                             | Required |
|-----------|---------------------------------------------------------|----------|
| `repo_url`| Full Forgejo mirror repository URL. Example: `https://git.example.com/owner/repo` | Yes      |
| `token`   | Forgejo API token with permission to trigger mirror sync | Yes      |

## Outputs

| Name     | Description                                      |
|----------|--------------------------------------------------|
| `domain` | Forgejo domain extracted from `repo_url`         |
| `owner`  | Repository owner extracted from `repo_url`       |
| `repo`   | Repository name extracted from `repo_url`          |

## Example with outputs

```yaml
jobs:
  mirror-sync:
    runs-on: self-hosted
    steps:
      - uses: YueMi-Development/Forgejo-Mirror-Sync@v1
        id: sync
        with:
          repo_url: ${{ secrets.MIRROR_REPO_URL }}
          token: ${{ secrets.FORGEJO_TOKEN }}

      - run: |
          echo "Synced ${{ steps.sync.outputs.owner }}/${{ steps.sync.outputs.repo }} on ${{ steps.sync.outputs.domain }}"
```

## Required secrets

- `MIRROR_REPO_URL` — the full URL of the Forgejo mirror repository.
- `FORGEJO_TOKEN` — a Forgejo API token with access to trigger the mirror sync endpoint.

## How it works

1. Parses `repo_url` into `domain`, `owner`, and `repo`.
2. Calls the Forgejo API endpoint:
   ```
   POST https://<domain>/api/v1/repos/<owner>/<repo>/mirror-sync
   ```
3. Fails the workflow step if the API returns a non-2xx status code.

## Releases

Releases are managed by the [Release workflow](.github/workflows/release.yml). Running it with version `1.0.0` creates:

- Tag `v1.0.0`
- Tag `v1` (floating major version)
- Branch `release/v1.0.0` (archive branch)

Use `@v1` to always get the latest non-breaking update, or pin to a specific version with `@v1.0.0`.

## License

See [LICENSE](./LICENSE).
