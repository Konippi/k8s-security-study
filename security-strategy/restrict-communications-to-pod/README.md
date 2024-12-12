# Restrict Communications to Pod

This is an example of how to restrict communications to a pod.

## Steps

1. Deploy pods and services.
    
#### wordpress

- LoadBalancer

    ```bash
    kubectl apply -f wordpress/service.yaml
    ```

- Pod

    ```bash
    kubectl apply -f wordpress/pod.yaml
    ```

#### mysql

- ClusterIP

    ```bash
    > kubectl apply -f mysql/service.yaml
    ```

- Pod

    ```bash
    > kubectl apply -f mysql/pod.yaml
    ```

#### vulnerable-pod

- Pod

    ```bash
    > kubectl apply -f vulnerable-pod/pod.yaml
    ```

2. Access the vulnerable pod.

    ```bash
    > kubectl exec -it vulnerable-pod -n network-policy-ns -- /bin/bash
    ```

3. Install `mysql-client`.

    ```bash
    > apt-get update
    > apt-get install mysql-client -y
    ```

4. Access the MySQL Pod.

    ```bash
    > mysql -h wordpress-mysql -u wordpress -ppassword
    ```

5. Access the WordPress Pod.

    ```bash
    > minikube tunnel # -> Access the External LoadBalancer IP
    ```

6. Apply network policies.

    ```bash
    > kubectl apply -f deny-all-network-policy.yaml
    > kubectl apply -f mysql/network-policy.yaml
    > kubectl apply -f wordpress/network-policy.yaml
    > kubectl apply -f vulnerable-pod/network-policy.yaml
    ```

7. Check the network policies.

    ```bash
    > kubectl exec -it vulnerable-pod -n network-policy-ns -- /bin/bash
    > mysql -h wordpress-mysql -u wordpress -ppassword
    ```

## Communication Requirements for Each pod

| Pod | Ingress | Egress |
| --- | --- | --- |
| WordPress Pod | to `80` Port | to `3306` Port in MySQL Pod & to `53` Port for Internal DNS |
| MySQL Pod | from Wordpress Pod to `3306` Port | - |
| Vulnerable Pod | to `80` Port | - |

