# Delete Unnecessary Capabilities

This strategy is about deleting unbecessary capabilities in k8s.

# How to Check the Granted Capabilities

1. Deploy a pod

    ```bash
    > kubectl run ubuntu -it --image=ubuntu:22.04 --rm --restart=Never -- /bin/bash
    ```

2. Install `libcap2-bin` package.

    ```bash
    > apt update && apt install -y libcap2-bin
    ```

3. Check the granted capabilities.

    ```bash
    > getpcaps $$
    1: cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap=ep

    > date -s "2000/01/01 00:00:00"
    date: cannot set date: Operation not permitted # Requires CAP_SYS_TIME
    Sat Jan  1 00:00:00 UTC 2000
    ```

## Limit the Capabilities

1. Deploy a pod with limited capabilities.

    ```bash
    > kubectl apply -f pod.yaml
    > kubectl exec -it capability -- /bin/bash
    ```

2. Install `libcap2-bin` package.

    ```bash
    > apt update && apt install -y libcap2-bin
    ```

3. Check the granted capabilities.

    ```bash
    > getpcaps $$
    8: cap_chown,cap_dac_override,cap_fowner,cap_setgid,cap_setuid,cap_net_bind_service=ep
    ```
