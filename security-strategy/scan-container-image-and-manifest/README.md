# Scan Container Image and Manifest

This is an example of how to scan a container image and k8s manifest using the [Trivy](https://trivy.dev/v0.57/).

## Steps

1. Scan the container image

    ```bash
    > trivy image ubuntu:20.04
    ```

2. Scan the k8s manifest

    ```bash
    > trivy config pod.yaml
    ```
