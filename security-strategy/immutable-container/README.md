# Immutable Container

This strategy is about creating an immutable container that cannot be modified after deployment.
Usually, OverlayFS created on the container host is mounted in the container root directory as the root file system with read-write status.

OverlayFS is composed of the following directories:

- `lowerdir`: the read-only directory that contains the original file system. (ex. container image)
- `upperdir`: the read-write directory that contains the changes made to the original file system. (ex. logs)
- `workdir`: the directory that supports the internal processing of OverlayFS. 

## Difference between mutable and immutable containers

1. Deploy mutable and immutable pods.

    ```bash
    > kubectl apply -f mutable-container.yaml
    > kubectl apply -f immutable-container.yaml
    ```

2. Check the difference between mutable and immutable containers.

    - Mutable Container
    
        ```bash
        > kubectl exec -it mutable -- touch tmp.txt
        ```
    
    - Immutable Container
    
        ```bash
        > kubectl exec -it immutable -- touch tmp.txt
        touch: cannot touch 'tmp.txt': Read-only file system
        command terminated with exit code 1
        ```

## For using an image that requires writing to the root file system

Ex. `httpd` image requires writing to the root file system for logging.

```yaml
spec:
  containers:
    securityContext:
        readOnlyRootFilesystem: true
    volumeMounts:
    - name: logs
        mountPath: /usr/local/apache2/logs
  volumes:
  - name: logs
    emptyDir: {}
```

1. Deploy a pod

    ```bash
    > kubectl apply -f minimum-required-mount-pod.yaml
    ```
