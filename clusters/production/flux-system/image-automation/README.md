# Flux Image Automation

This directory contains configuration for automated image updates using Flux.

## Overview

Flux Image Automation allows you to:
- Scan container registries for new image tags
- Automatically update image references in Git
- Follow semantic versioning or other policies
- Keep your deployments up-to-date

## Components

### ImageRepository
Scans a container registry for available image tags.

### ImagePolicy
Defines rules for selecting which image tag to use (e.g., semver, alphabetical).

### ImageUpdateAutomation
Configures automatic commits to Git when new images are found.

## Example Usage

### 1. Create an ImageRepository

```yaml
apiVersion: image.toolkit.fluxcd.io/v1beta2
kind: ImageRepository
metadata:
  name: radarr
  namespace: flux-system
spec:
  image: ghcr.io/linuxserver/radarr
  interval: 10m
```

### 2. Create an ImagePolicy

```yaml
apiVersion: image.toolkit.fluxcd.io/v1beta2
kind: ImagePolicy
metadata:
  name: radarr
  namespace: flux-system
spec:
  imageRepositoryRef:
    name: radarr
  policy:
    semver:
      range: '>=5.0.0'
```

### 3. Add Image Marker to HelmRelease

In your HelmRelease values:

```yaml
spec:
  values:
    image:
      repository: ghcr.io/linuxserver/radarr
      tag: 5.0.0  # {"$imagepolicy": "flux-system:radarr:tag"}
```

### 4. Configure ImageUpdateAutomation

See `image-update-automation.yaml` for the automation configuration.

## Security Considerations

- Image automation requires write access to your Git repository
- Use a dedicated deploy key with minimal permissions
- Consider using approval workflows for production updates
- Test image updates in staging environments first

## Enable Image Automation

1. Install Flux with image automation components:
   ```bash
   flux bootstrap github \
     --components-extra=image-reflector-controller,image-automation-controller \
     --owner=<your-github-user> \
     --repository=flux \
     --branch=main \
     --path=clusters/production
   ```

2. Create a Git deploy key with write access:
   ```bash
   flux create secret git flux-system \
     --url=ssh://git@github.com/<your-github-user>/flux \
     --private-key-file=<path-to-private-key>
   ```

3. Uncomment the resources in `kustomization.yaml`

## References
- [Flux Image Automation Guide](https://fluxcd.io/docs/guides/image-update/)
- [ImagePolicy Specification](https://fluxcd.io/docs/components/image/imagepolicies/)
