# Codesphere Deployment action

This action prints "Hello World" or "Hello" + the name of a person to greet to the log.

## Inputs

### `email`

**Required** email of the codesphere user.

### `password`

**Required** Password of the codesphere user account.

### `team`

**Required** Name of the codesphere team.

### `plan`

The name of the person to greet. Default `"Boost"`.

## Example usage

```yaml
uses: codesphere-cloud/gh-action-deploy@v0.1
with:
  email: 'bot@example.com'
  password: '123'
  team: 'MyTeam'
  plan: 'Boost'
```

```yaml
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
        uses: codesphere-cloud/gh-action-deploy@v0.1
        env:
          GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
        with:
            email: ${{ secrets.CS_EMAIL }}
            password: ${{ secrets.CS_PASSWORD }}
            team: 'My Team'
            plan: 'Boost'
```

