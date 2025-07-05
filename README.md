# Flux GitOps Repository

This repository contains the Kubernetes cluster configuration for my infrastructure, managed declaratively using [FluxCD](https://fluxcd.io/).

## Overview

This is a GitOps repository that defines the entire state of a Kubernetes cluster. Flux continuously monitors this repository and applies any changes to the cluster, ensuring that the defined state in Git is the same as the running state in the cluster.

## Core Technologies

* **[Kubernetes](https://kubernetes.io/)**: The container orchestration platform.
* **[FluxCD](https://fluxcd.io/)**: The GitOps tool for keeping the cluster in sync with this repository.
* **[Kustomize](https://kustomize.io/)**: Used to customize Kubernetes resource manifests.
* **[Helm](https://helm.sh/)**: Used via Flux's `HelmRelease` controller to manage third-party applications.
* **[SOPS](https://github.com/getsops/sops)**: For encrypting secrets in Git, using `age` as the backend.

## Repository Structure

The repository is organized into the following main directories:

* `clusters/production/`: This is the main entry point for Flux. It contains the Kustomization that bootstraps the entire cluster, referencing the infrastructure and application layers.
* `infrastructure/`: Contains the Kubernetes manifests for core infrastructure services. Each service is organized into its own directory.
    * **cert-manager**: For automated TLS certificate management.
    * **Longhorn**: For persistent, replicated block storage.
    * **MetalLB**: For providing LoadBalancer services in a bare-metal environment.
    * **Traefik**: As the Ingress controller.
* `applications/`: Contains the manifests for applications running on the cluster.

## Secrets Management

Secrets in this repository are encrypted using [SOPS](https://github.com/getsops/sops) with [age](https://github.com/FiloSottile/age). The `.sops.yaml` file defines the encryption rules. To decrypt or edit secrets, you will need the private `age` key, which is not stored in this repository.
