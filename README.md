# Unlock Environment Action

[![CI](https://github.com/flxbl-io/unlock-environment/actions/workflows/ci.yml/badge.svg)](https://github.com/flxbl-io/unlock-environment/actions/workflows/ci.yml)

A GitHub Action that unlocks an environment via [SFP Server](https://docs.flxbl.io/sfp-server). This action is designed to never fail the pipeline, making it safe to use in cleanup steps.

## Usage

### Basic Usage

```yaml
- name: Unlock Environment
  uses: flxbl-io/unlock-environment@v1
  with:
    environment: dev-sandbox
    ticket-id: ${{ steps.lock.outputs.ticket-id }}
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}
```

### In a Workflow with Lock/Unlock Pattern

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    container: ghcr.io/flxbl-io/sfops:latest

    steps:
      - uses: actions/checkout@v4

      - name: Lock Environment
        id: lock
        uses: flxbl-io/lock-environment@v1
        with:
          environment: dev-sandbox
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

      - name: Deploy
        run: |
          sfp deploy -o dev-sandbox

      - name: Unlock Environment
        if: always()
        uses: flxbl-io/unlock-environment@v1
        with:
          environment: dev-sandbox
          ticket-id: ${{ steps.lock.outputs.ticket-id }}
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `environment` | Name of the environment to unlock | Yes | - |
| `ticket-id` | Lock ticket ID obtained from lock operation | Yes | - |
| `repository` | Repository name (`owner/repo` format) | No | Current repository |
| `sfp-server-url` | URL to SFP Server (e.g., `https://your-org.flxbl.io`) | Yes | - |
| `sfp-server-token` | SFP Server application token | Yes | - |

## Behavior

This action is designed to **never fail the pipeline**. If the unlock operation fails for any reason (network issues, invalid ticket, etc.), the action will:

1. Log a warning message
2. Exit successfully (exit code 0)

This design ensures that unlock operations in cleanup steps (`if: always()`) don't cause unnecessary pipeline failures.

## Prerequisites

- **SFP Server**: Environment must be registered in SFP Server
- **Valid Ticket**: The ticket-id must match an active lock
- **Runtime**: `sfp` CLI must be available (use `sfops` Docker image)

## Example: Complete Environment Lock Pattern

```yaml
name: Deploy to Environment

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        type: choice
        options:
          - dev
          - staging
          - uat

jobs:
  deploy:
    runs-on: ubuntu-latest
    container: ghcr.io/flxbl-io/sfops:latest

    steps:
      - uses: actions/checkout@v4

      - name: Lock Environment
        id: lock
        uses: flxbl-io/lock-environment@v1
        with:
          environment: ${{ inputs.environment }}
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

      - name: Deploy to Environment
        run: |
          sfp deploy -o ${{ inputs.environment }}

      - name: Run Tests
        run: |
          sfp test -o ${{ inputs.environment }}

      - name: Unlock Environment
        if: always()
        uses: flxbl-io/unlock-environment@v1
        with:
          environment: ${{ inputs.environment }}
          ticket-id: ${{ steps.lock.outputs.ticket-id }}
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}
```

## License

Copyright 2025 flxbl-io. All rights reserved. See [LICENSE](LICENSE) for details.

## Support

- [Documentation](https://docs.flxbl.io)
- [Issues](https://github.com/flxbl-io/unlock-environment/issues)
- [flxbl.io](https://flxbl.io)
