# Restrict Container Execution Users

This strategy is about designating a container execution user for restricting the user that containers run as.

## Steps

1. Build a container image that runs as a non-root user (`k8s-user`).

    ```bash
    > docker buildx build -t designate-user-container-image .
    ```

2. Load the local images to minikube

    ```bash
    > minikube image load designate-user-container-image:latest
    ```

3. Deploy pods as users authorized or unauthorized.

    ```bash
    > kubectl apply -f authorized-user-pod.yaml
    > kubectl apply -f unauthorized-user-pod.yaml
    ```

4. Check the difference between authorized and unauthorized users.

    ```bash
    > k logs authorized-user
    > k logs unauthorized-user
    /bin/sh: 1: cannot create author.txt: Permission denied
    /bin/sh: 1: cannot create author.txt: Permission denied
    /bin/sh: 1: cannot create author.txt: Permission denied
    ```
