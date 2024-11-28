# k8s-introduction

This is a simple introduction to Kubernetes.

## Steps

1. Create a pod.

    ```sh
    > kubectl apply -f pod.yaml
    ```

2. Create a Load Balancer.

    ```sh
    > kubectl apply -f service.yaml
    ```

3. Assign the external IP to the load balancer.

    ```sh
    > minikube tunnel
    ```

4. Get the load balancer IP.

    ```sh
    > kubectl get service -n nginx
    ```

5. Access the load balancerl

    ```sh
    > curl http://${EXTERNAL-IP}
    ```
