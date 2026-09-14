# Codesphere Deployment Action

This action creates a preview environment of your repository in Codesphere.

## :warning: Prerequisites

- If you don't have an account on Codesphere yet, create one at https://cloud.codesphere.com or your private cloud url
- Connect your user account with GitHub and allow access to your repository.
- Create an api token at https://cloud.codesphere.com/ide/user/public-api-keys for authentication (replace cloud.codesphere.com with your private cloud url if you want to use it with your private cloud instance)

## Inputs

### `apiToken`

**Required** api token of the codesphere user.

### `team`

**Required** Name of the codesphere team.

### `plan`

Plan of the workspaces ide service. All landscape services are deployed with the plan configured in your selected ci profile

Available options:
- Micro
- Boost
- Pro

Default: Smallest plan

### `onDemand`

Use a Codesphere onDemand workspace that shuts down if unused.

Default `"false"`.

### `restricted`

Whether the dev domain of the workspace is restricted to team members of the workspace or public.

Default `"false"`.

### `cloneDepth`

Whether the repository clone should be shallow or not.

### `skipLfs`

Whether to bypass the automatic downloading (smudging) of Git LFS files during git clone.

Default `"false"`.

### `recurseSubmodules`

After the clone is created, initialize and clone all submodules.

Default `"true"`.

### `deploymentLinkType`

Controls the URL format posted in PR comments.

Available options:
- `dev-domain` (default): direct link to the running app
- `preview`: link to the IDE preview tab

### `ciProfile`

The name of the CI profile to use for the deployment.
If not provided - default profile will be used.

### `env`

Set environment variables in your workspace.

Use dotenv like environment variables definition.
See https://www.npmjs.com/package/dotenv for details.

### `vpnConfig`

Name of the vpn config the workspace should connect to.
The vpn configuration has to be configured in the team before.

### `apiUrl`

Base domain of the target Codesphere instance (with https://), e.g. `https://codesphere.com`, or `https://my-custom-codesphere.com`

### `sharedVaultName` (optional)

Name of the shared vault to attach to the created workspace.
The shared vault has to be configured in the team before.

When set, the preview deployment reuses the secrets of this shared vault instead
of maintaining its own isolated set, so secrets can be maintained once and
shared across all preview workspaces.


## Example usage

This integration can either be used as an action or as a workflow.

#### Action

```yaml
# .github/workflows/codesphere.yaml
uses: codesphere-cloud/gh-action-deploy@main
with:
  apiToken: 'xxx'
  team: 'MyTeam'
  plan: 'Boost'
  env: |
    MY_ENV=test
```

#### Workflow

```yaml
# .github/workflows/codesphere.yaml
on:
  workflow_dispatch:
  # open, reopen and synchronize will deploy a workspace for the current commit.
  # If a workspce is already deployed, that workspace is updated to the newest version.
  #
  # closed: Workspace will be deleted
  pull_request:
    types:
    - closed
    - opened
    - reopened
    - synchronize

permissions:
  contents: read
  pull-requests: read
  deployments: write

jobs:
  deploy:
    # prevent multiple workspaces to be created for the same branch
    concurrency: codesphere
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Deploy
        uses: codesphere-cloud/gh-action-deploy@main
        env:
          GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
        with:
            apiToken: ${{ secrets.CS_API_TOKEN }}
            team: 'My Team'
            plan: 'Boost'
            env: |
              MY_ENV=test
              MY_SECRET=${{ secrets.MY_SECRET }}
```

## Use with private submodules

The workflows above use the GitHub action access token to clone and update the repository (See `GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}`).
`${{secrets.GITHUB_TOKEN}}` is scoped to the current repository, so if you have private submodules you will need to provide your own [PAT](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line).

1. Create your own [private access token](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line).
2. Create a secret called `PAT`.
3. Update your workflow to use that token by replacing `GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}` with `GITHUB_TOKEN: ${{secrets.PAT}}`
