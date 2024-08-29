# Auto-prep Flipper App Catalog updates
Unofficial. Use with care; app catalog PRs are manually reviewed by the Flipper team, so one should avoid spamming them with spurious or frivolous PRs.

First-time addition of an app to the catalog must be done manually, this simply updates an already existing app manifest with the details of the current commit, staging changes in a branch of one's fork of the `flipperdevices/flipper-application-catalog` repo. 

## Usage

```yml
name: Update Flipper Application Catalog

on:
  workflow_dispatch:
  push:
    branches:
      - main
    paths:
      - 'application.fam'

jobs:
  update-catalog:
    runs-on: ubuntu-latest

    steps:
      - uses: zacharyweiss/flipper-app-catalog-action@v1.2
        id: update_manifest
        with:
          CATALOG_UPDATE_TOKEN: ${{ secrets.CATALOG_UPDATE_TOKEN }}

    # Auto-PR logic here, or manually initiate PR from the branch created in your App Catalog fork.
```
Usage example can be found in the [MagSpoof GH workflow](https://github.com/zacharyweiss/magspoof_flipper/blob/main/.github/workflows/app_catalog_upd.yml).

### Action inputs

All inputs except `CATALOG_UPDATE_TOKEN` are **optional**. If not set, sensible defaults will be used.

| Name | Description | Default |
| --- | --- | --- |
| `CATALOG_UPDATE_TOKEN` | `repo` scoped [Personal Access Token (PAT)](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token). | |
| `CATALOG_REPO` | Name of App Catalog repo | `flipper-application-catalog` |
| `CATALOG_FORK_OWNER` | Owner of App Catalog fork, in which to stage PR branch | `${{ github.repository_owner }}` |
| `CATALOG_UPSTREAM_OWNER` | Owner of upstream App Catalog, to which PRs are opened | `flipperdevices` |
| `COMMIT_USERNAME` | Name to use for automated commits | `${{ github.actor }}` |
| `COMMIT_EMAIL` | Email to use for automated commits | `${{ github.actor_id }}+${{ github.actor }}@users.noreply.github.com` |
| `GIT_LOG_PRETTY_FORMAT` | Format for git log pretty-printing | `"%as %h (%an) %s"` |
| `DRY_RUN` | For action testing purposes only; fills example data in manifest.yml for apps not yet in the catalog, instead of updating a preexisting manifest | `false` |
| `VALIDATE_BUNDLE` | Run bundle validation before pushing branch | `true` |

### Action outputs

| Name | Description |
| --- | --- |
| `V_MAJOR` | FAP version major |
| `V_MINOR` | FAP version minor |
| `NAME` | Sanitized FAP name (drops brackets, strips whitespace) |
| `APPID` | FAP appid |
| `BRANCH_NAME` | Name of to-be-PR'd branch in App Catalog fork |
| `MANIFEST_PATH` | Path to manifest.yml in App Catalog |
| `CHANGES` | Changes between old and new commit SHAs, per `git log` |
| `OLD_SHA` | Old commit SHA in manifest.yml |
