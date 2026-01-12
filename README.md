# Unlock Environment Action

[![CI](https://github.com/flxbl-io/unlock-environment/actions/workflows/ci.yml/badge.svg)](https://github.com/flxbl-io/unlock-environment/actions/workflows/ci.yml)

A GitHub Action that unlocks an environment via [SFP Server](https://docs.flxbl.io/sfp-server). This action is designed to never fail the pipeline, making it safe to use in cleanup steps.

**Note**: If you're using [auth-environment-with-lock](https://github.com/flxbl-io/auth-environment-with-lock) with `auto-unlock: true` (the default), you don't need this action - environments are automatically unlocked when the workflow completes.

## When to Use This Action

Use this action when:
- You disabled auto-unlock (`auto-unlock: false`) in `auth-environment-with-lock`
- You need to unlock an environment from a different job
- You want explicit control over when the unlock happens

## Usage

### Basic Usage

```yaml
- name: Lock environment (disable auto-unlock)
  id: lock
  uses: flxbl-io/auth-environment-with-lock@v1
  with:
    environment: 'QA'
    auto-unlock: 'false'
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

- name: Install packages
  run: sfp install -o ${{ steps.lock.outputs.alias }}

- name: Unlock Environment
  if: always()
  uses: flxbl-io/unlock-environment@v1
  with:
    environment: 'QA'
    ticket-id: ${{ steps.lock.outputs.ticket-id }}
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `environment` | Name of the environment to unlock | **Yes** | - |
| `ticket-id` | Lock ticket ID from auth-environment-with-lock | **Yes** | - |
| `sfp-server-url` | URL to SFP Server (e.g., `https://your-org.flxbl.io`) | **Yes** | - |
| `sfp-server-token` | SFP Server application token | **Yes** | - |
| `repository` | Repository name (`owner/repo` format) | No | Current repository |

## Behavior

This action is designed to **never fail the pipeline**. If the unlock operation fails for any reason (network issues, invalid ticket, etc.), the action will:

1. Log a warning message
2. Exit successfully (exit code 0)

This ensures that unlock operations in cleanup steps (`if: always()`) don't cause unnecessary pipeline failures.

## Example: Cross-Job Unlock

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    container: ghcr.io/flxbl-io/sfops:latest
    outputs:
      ticket-id: ${{ steps.lock.outputs.ticket-id }}

    steps:
      - uses: actions/checkout@v4

      - name: Lock Environment
        id: lock
        uses: flxbl-io/auth-environment-with-lock@v1
        with:
          environment: 'staging'
          auto-unlock: 'false'  # We'll unlock in a separate job
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

      - name: Install packages
        run: sfp install -o ${{ steps.lock.outputs.alias }}

  cleanup:
    runs-on: ubuntu-latest
    container: ghcr.io/flxbl-io/sfops:latest
    needs: deploy
    if: always()

    steps:
      - name: Unlock Environment
        uses: flxbl-io/unlock-environment@v1
        with:
          environment: 'staging'
          ticket-id: ${{ needs.deploy.outputs.ticket-id }}
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}
```

## Prerequisites

- **SFP Server**: Environment must be registered in SFP Server
- **Valid Ticket**: The ticket-id must match an active lock
- **Runtime**: `sfp` CLI must be available (use `sfops` Docker image)

## Related Actions

- [auth-environment-with-lock](https://github.com/flxbl-io/auth-environment-with-lock) - Authenticate with exclusive locking (has auto-unlock)
- [auth-environment](https://github.com/flxbl-io/auth-environment) - Authenticate without locking

## License

Copyright 2025 flxbl-io. All rights reserved. See [LICENSE](LICENSE) for details.
