# Load Balancer

## Steps to create a Load Balancer

1. Create a pod

    ```sh
    > kubectl apply -f pod.yaml
    ```

2. Create a Load Balancer

    ```sh
    > kubectl apply -f load-balancer.yaml
    ```

3. Allocate external IP to the Load Balancer

    ```sh
    > minikube tunnel
    ```

4. Get the Load Balancer IP

    ```sh
    > kubectl get service -n nginx-namespace
    ```

5. Access the Load Balancer

    ```sh
    > curl http://<EXTERNAL-IP>
    ```
