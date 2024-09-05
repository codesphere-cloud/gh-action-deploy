# Codesphere Deployment Action

This action creates a preview environment of your repository in Codesphere.

## :warning: Prerequisites

- Configure a user in Codesphere https://codesphere.com with username and password.
- Connect your user account with GitHub and allow access to your repository.

## Inputs

### `email`

**Required** email of the codesphere user.

### `password`

**Required** Password of the codesphere user account.

### `team`

**Required** Name of the codesphere team.

### `plan`

Plan of the created workspace. 

Available options:
- Micro
- Boost
- Pro

Default `"Boost"`.

### `env`

Set environment variables in your workspace.
      
Use dotenv like environment variables definition.
See https://www.npmjs.com/package/dotenv for details.

### `vpnConfig`

Name of the vpn config the workspace should connect to.
The vpn configuration has to be configured in the team before.


## Example usage

This integration can either be used as an action or as a workflow.

#### Action

```yaml
# .github/workflows/codesphere.yaml
uses: codesphere-cloud/gh-action-deploy@main
with:
  email: 'bot@example.com'
  password: '123'
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
            email: ${{ secrets.CS_EMAIL }}
            password: ${{ secrets.CS_PASSWORD }}
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
