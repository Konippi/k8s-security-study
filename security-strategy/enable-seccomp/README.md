# Enable Seccomp

This strategy enables Seccomp to restrict the system calls available to a process.

## Steps

1. Deploy a pod with Seccomp enabled.

    ```bash
    > kubectl apply -f default-seccomp-pod.yaml
    > kubectl exec -it default-seccomp-pod -- /bin/bash
    ```
2. Check the available system calls.

    ```bash
    > unshare -r /bin/bash
    unshare: unshare failed: Operation not permitted
    ```
