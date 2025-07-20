# Flux GitOps Repository

This repository contains the Kubernetes cluster configuration for my infrastructure, managed declaratively using [FluxCD](https://fluxcd.io/).

## Overview

This is a GitOps repository that defines the entire state of a Kubernetes cluster. Flux continuously monitors this repository and applies any changes to the cluster, ensuring that the defined state in Git is the same as the running state in the cluster.

The cluster is built on a foundation of core services that provide networking, storage, and security, with applications layered on top.

## Core Tooling

This project relies on a set of essential tools to build and manage the Kubernetes cluster declaratively:

* **[Kubernetes](https://kubernetes.io/)**: The container orchestration platform.
* **[FluxCD](https://fluxcd.io/)**: The GitOps tool for keeping the cluster in sync with this repository.
* **[Kustomize](https://kustomize.io/)**: Used to customize Kubernetes resource manifests.
* **[Helm](https://helm.sh/)**: Used via Flux's `HelmRelease` controller to manage third-party applications.
* **[SOPS](https://github.com/getsops/sops)**: For encrypting secrets in Git, using `age` as the backend.

## Core Infrastructure

The following core infrastructure services are deployed to support applications running on the cluster:

* **[Traefik](https://traefik.io/traefik/)**: As the Ingress controller, managing external access to cluster services.
* **[MetalLB](https://metallb.universe.tf/)**: Provides `LoadBalancer` services in a bare-metal environment, assigning external IPs to services like Traefik.
* **[cert-manager](https://cert-manager.io/)**: For automated TLS certificate management from Let's Encrypt using Cloudflare for DNS01 challenges.
* **[Longhorn](https://longhorn.io/)**: For persistent, replicated block storage (`StorageClass: longhorn`).
* **[CloudNativePG (CNPG)](https://cloudnative-pg.io/)**: For managing PostgreSQL database clusters required by various applications.

## Repository Structure

The repository is organized into the following main directories under `clusters/production/`:

* `flux-system/`: Contains the core FluxCD component manifests and the main synchronization configuration that points to this repository.
* `core/`: Contains the Kubernetes manifests for the core infrastructure services listed above.
* `base/`: Holds base-level cluster configurations, such as namespaces and core storage definitions.
* `apps/`: Contains the manifests for applications running on the cluster.

Flux bootstraps the cluster using the kustomizations defined in `clusters/production/`, starting with `core.yaml`, then `base.yaml`, and finally `apps.yaml`, respecting the `dependsOn` relationships to ensure correct deployment order.

## Secrets Management

Secrets in this repository are encrypted using [SOPS](https://github.com/getsops/sops) with [age](https://github.com/FiloSottile/age). The `.sops.yaml` file defines the encryption rules for all `.yaml` files. Any resource containing a `data` or `stringData` field will be automatically encrypted by SOPS.

To decrypt or edit secrets, you will need the private `age` key, which is not stored in this repository and is protected by `.gitignore`.
