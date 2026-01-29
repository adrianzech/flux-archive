# Flux Notifications

This directory contains notification configurations for Flux CD.

## Providers

Flux supports multiple notification providers:
- **Discord**: Real-time notifications to Discord channels
- **Slack**: Real-time notifications to Slack channels
- **Microsoft Teams**: Notifications to Teams channels
- **Generic Webhook**: Custom webhook notifications

## Setup Instructions

### Discord Setup

1. Create a webhook in your Discord server:
   - Server Settings → Integrations → Webhooks → New Webhook
   - Copy the webhook URL

2. Create a secret with your webhook URL:
   ```bash
   kubectl create secret generic discord-webhook \
     --namespace=flux-system \
     --from-literal=address=https://discord.com/api/webhooks/YOUR_WEBHOOK_URL
   ```

3. Encrypt the secret with SOPS:
   ```bash
   kubectl get secret discord-webhook -n flux-system -o yaml > discord-secret.yaml
   sops --encrypt --in-place discord-secret.yaml
   ```

4. Uncomment the provider in `discord-provider.yaml`
5. Uncomment the `providerRef` in `alerts.yaml`

### Slack Setup

1. Create a webhook in your Slack workspace:
   - Go to https://api.slack.com/apps
   - Create a new app or select existing
   - Enable Incoming Webhooks
   - Create a webhook for your channel

2. Create a secret with your webhook URL:
   ```bash
   kubectl create secret generic slack-webhook \
     --namespace=flux-system \
     --from-literal=address=https://hooks.slack.com/services/YOUR_WEBHOOK_URL
   ```

3. Encrypt the secret with SOPS:
   ```bash
   kubectl get secret slack-webhook -n flux-system -o yaml > slack-secret.yaml
   sops --encrypt --in-place slack-secret.yaml
   ```

4. Uncomment the provider in `slack-provider.yaml`
5. Uncomment the `providerRef` in `alerts.yaml`

## Alert Levels

### Error Alerts
- Always enabled
- Triggers on reconciliation failures
- Notifies about critical issues requiring attention

### Info Alerts
- Disabled by default (set `suspend: true`)
- Triggers on successful reconciliations
- Useful for tracking deployments
- Can be noisy - enable with caution

## Testing Notifications

After setup, trigger a test notification:

```bash
flux reconcile kustomization flux-system --with-source
```

## References
- [Flux Notifications Documentation](https://fluxcd.io/docs/guides/notifications/)
- [Provider Specifications](https://fluxcd.io/docs/components/notification/provider/)
