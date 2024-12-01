# Strip Unnecessary Authority

This is an example for removing unnecessary authority from a pod.

## Attack Scenario

1. Deploy `Namespace`, `ClusterRoleBinding`, `ServiceAccount`, `Pod` to the cluster.

    ```bash
    > kubectl apply -f namespace.yaml
    > kubectl apply -f cluster-role-binding.yaml
    > kubectl apply -f service-account.yaml
    > kubectl apply -f pod.yaml
    ```

2. Access the pod.

    ```bash
    > kubectl exec -it unnecessary-authority-pod -n unnecessary-authority -- /bin/bash
    ```

3. Install `kubectl` in the pod.

    ```bash
    > apt-get update
    > apt-get install -y curl
    > curl -LO https://dl.k8s.io/release/v1.30.0/bin/linux/amd64/kubectl
    ```

4. Access the Kubernetes API server from the pod.

    ```bash
    > chmod +x kubectl
    > mv ./kubectl /usr/local/bin/kubectl
    > kubectl get pod
    NAME                        READY   STATUS    RESTARTS   AGE
    unnecessary-authority-pod   1/1     Running   0          124m
    ```

## Solutions

- Use default service account.
- Set `automountServiceAccountToken` to `false` in the pod spec.
