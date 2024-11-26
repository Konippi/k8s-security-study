# Leak Container Image :scream:

This is an example of an attack caused by a leak container image.

## Requirements

- CPU Architecture
  - amd64
  - x86_64
  - arm64
- Tools
  - Docker
  - kubectl
  - minikube

## Attack Scenario

1. Build the leak container image

    ```bash
    > docker buildx build -t leak-container-image .
    ```

2. Save the leak container image as a tar file

    ```bash
    > docker save leak-container-image:latest -o leak-container-image.tar
    ```

3. Extract the leak container image from the tar file

    ```bash
    > tar xvf leak-container-image.tar -C ./leak
    ```

4. Load the layer of the leak container image

    ```bash
    > for layer in $(cat leak/manifest.json | jq -c '.[0].Layers[]' | sed s/\"//g); do \
    layer_dir=leak/blobs/sha256/layer-$(eval echo ${layer} | awk '{sub("blobs/sha256/", "");print $0;}') ; \
    mkdir ${layer_dir} ; \
    tar xvf leak/${layer} -C ${layer_dir} ; done
    ```
