# Reliable Container Image :closed_lock_with_key:

This repository is an example of how to make the container image reliable.

## Requirements

- CPU Architecture
  - amd64
  - x86_64
  - arm64
- Tools
  - Docker
  - kubectl
  - minikube
  - [sigstore/cosign](https://github.com/sigstore/cosign)
  - [helm](https://github.com/helm/helm)

## Initial Setup

- Install sigstore/cosign

    ```bash
    > brew install cosign
    ```

- Install helm

    ```bash
    > brew install helm
    ```

## Sign the Container Image

1.Setup for pulling the image from the private repository in the container registry

    ```bash
    > kubectl create secret docker-registry regcred \
    --docker-username=${DOCKER_USERNAME} \
    --docker-password=${DOCKER_PASSWORD} \
    --docker-email=${DOCKER_EMAIL} \
    ```

2. Build the container image

    ```bash
    > docker build -t reliable-container-image .
    ```

3. Push the container image to the container registry

    ```bash
    > docker tag reliable-container-image:latest ${DOCKER_ID}/reliable-container-image:latest
    > docker push ${DOCKER_ID}/reliable-container-image:latest
    ```

4. Create a pod

    ```bash
    > IMAGE=${DOCKER_ID}/reliable-container-image:latest@${DIGEST} envsubst < pod.yaml | k apply -f -
    ```

5. Install sigstore/cosign

    ```bash
    > brew install cosign
    ```

6. Generate a key pair for signing

    ```bash
    > cosign generate-key-pair
    ```

7. Sign the container image in the container registry

    ```bash
    > cosign sign --key cosign.key ${DOCKER_ID}/reliable-container-image:latest@${DIGEST}
    ```

8. Verify the container image by CLI

    ```bash
    > cosign verify --key cosign.pub ${DOCKER_ID}/reliable-container-image:latest | jq
    ```

## Sign the container image with k8s

1. Setup for pulling the image from the private repository in the container registry

    ```bash
    > kubectl create secret docker-registry regcred \
    --docker-username=${DOCKER_USERNAME} \
    --docker-password=${DOCKER_PASSWORD} \
    --docker-email=${DOCKER_EMAIL} \
    -n verify
    ```

2. Install Policy Controller with helm

    ```bash
    > helm repo add sigstore https://sigstore.github.io/helm-charts
    > helm repo update
    > helm install policy-controller sigstore/policy-controller \
    --create-namespace -n cosign-system \
    --version 0.7.0 \
    --devel
    ```

3. Create a secret for the key pair

    ```bash
    > kubectl create secret generic cosign-secret \
    --from-file=cosign.pub=./cosign.pub \
    -n cosign-system
    ```

4. Apply ClusterImagePolicy and Namespace

    ```bash
    > k apply -f manifest/cip.yaml
    > k apply -f manifest/namespace.yaml
    ```

5. Create a pod with verification

    ```bash
    # Signed pod
    > k apply -f manifest/signed-pod.yaml

    # Unsigned pod
    > k apply -f manifest/unsigned-pod.yaml
    ```
