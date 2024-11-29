# Resource Based Isolation

This is an example of how to isolate resources in k8s environment.

## Limited Resources

1. Build a container image implementing http server.

    ```bash
    > docker buildx build -t limited-resources-container-image .

2. Load the image into minikube.

    ```bash
    > minikube image load limited-resources-container-image
    ```

3. Deploy pods with unlimited resources.

    ```bash
    > kubectl apply -f manifest/limited-resources/foo-web/pod.yaml
    > kubectl apply -f manifest/limited-resources/bar-web/pod.yaml
    ```

4. Deploy load balancers.

    ```bash
    > kubectl apply -f manifest/limited-resources/foo-web/service.yaml
    > kubectl apply -f manifest/limited-resources/bar-web/service.yaml
    ```

5. Assign the external IP to the Load Balancer.

    ```bash
    > minikube tunnel
    ```

6. Access the deployed pods.

    ```bash
    # `foo-web` pod
    > curl ${FOO_WEB_EXTERNAL_IP}:8001 -w "time: %{time_total}\n"
    time: 4.300555

    # `bar-web` pod
    > curl ${BAR_WEB_EXTERNAL_IP}:8002 -w "time: %{time_total}\n"
    time: 4.710099
    ```

7. Send a large number of requests to the `foo-web` pod.

    ```bash
    > for i in {1..10}; \
      do \
        while true; do curl ${FOO_WEB_EXTERNAL_IP}:8001 > /dev/null; done & \
      done

    # `foo-web` pod
    > curl ${FOO_WEB_EXTERNAL_IP}:8001 -w "time: %{time_total}\n"
    time: 33.299555

    # `bar-web` pod (also be poorly influenced)
    > curl ${BAR_WEB_EXTERNAL_IP}:8002 -w "time: %{time_total}\n"
    time: 26.710099

    > for i in {1..10}; do kill %$i; done
    ```

8. Deploy pods with limited resources.

    ```bash
    > kubectl delete pod foo-web bar-web
    > kubectl apply -f manifest/limited-resources/foo-web/limited-resources-pod.yaml
    > kubectl apply -f manifest/limited-resources/bar-web/limited-resources-pod.yaml
    ```

9. Send a large number of requests to the `foo-web` pod.

    ```bash
    > for i in {1..10}; \
      do \
        while true; do curl ${FOO_WEB_EXTERNAL_IP}:8001 > /dev/null; done & \
      done

    # `foo-web` pod
    > curl ${FOO_WEB_EXTERNAL_IP}:8001 -w "time: %{time_total}\n"
    time: 35.271834

    # `bar-web` pod (also be poorly influenced)
    > curl ${BAR_WEB_EXTERNAL_IP}:8002 -w "time: %{time_total}\n"
    time: 4.921067

    > for i in {1..10}; do kill %$i; done
    ```

## Limit Range

1. Deploy a pod with namespace and limit range.

    ```bash
    > kubectl apply -f manifest/limit-range/namespace.yaml
    > kubectl apply -f manifest/limit-range/limit-range.yaml
    > kubectl apply -f manifest/limit-range/pod.yaml
    ```

2. Check the resource limits and requests of the pod.

    ```bash
    > kubectl describe pod limit-range-pod -n example-web
    ...
    Limits:
      cpu:     500m
      memory:  512Mi
    Requests:
      cpu:        250m
      memory:     256Mi
    ...
    ```

## Taint node

1. Add a node to the minikube.

    ```bash
    > minikube node add --worker 2
    ```

2. Grant the node a taint.

    ```bash
    > kubectl taint nodes minikube-m02 dedicated=true:NoSchedule
    ```

3. Set a label to the node.
   
    ```bash
    > kubectl label node minikube-m02 node-restriction.kubernetes.io/dedicated=true
    ```

4. Deploy a pod with a taint node.

    ```bash
    > kubectl apply -f manifest/taint-node/pod.yaml
    NAME         READY   STATUS              RESTARTS   AGE   IP       NODE           NOMINATED NODE   READINESS GATES
    taint-node   0/1     ContainerCreating   0          6s    <none>   minikube-m02   <none>           <none>
    ```
