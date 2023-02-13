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
