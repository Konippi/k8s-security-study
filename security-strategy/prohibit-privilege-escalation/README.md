# Prohibit Privilege Escalation

This is an example of how to prohibit privilege escalation in k8s.

## Steps

1. Build a container image that runs as a non-root user (`k8s-user`).

    ```bash
    > docker buildx build -t designate-user-container-image .
    ```

2. Load the local images to minikube

    ```bash
    > minikube image load privilege-escalation-container-image
    ```
3. Deploy a pod.

    ```bash
    > kubectl apply -f pod.yaml
    > kubectl exec -it privilege-escalation-false -- /bin/bash
    ```

4. Ensure that privilege escalation is prohibited.

    ```bash
    > kubectl exec -it privilege-escalation-false -- /bin/bash
    > sudo su -
    sudo: The "no new privileges" flag is set, which prevents sudo from running as root.
    sudo: If sudo is running in a container, you may need to adjust the container configuration to disable the flag.
    ```
