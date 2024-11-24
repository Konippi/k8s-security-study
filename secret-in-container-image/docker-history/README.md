# Leakage of secret by `docker history` :key:

this is an example of how to leak a secret by `docker history` command.

## Steps

1. Build a docker image with a secret.

    ```bash
    > docker build --build-arg PASSWORD=${OPTIONAL_PASSWORD} -t docker-history-container-image .
    ```

2. Run `docker history` command to see the history of the image.

    ```bash
    > docker history docker-history-container-image:latest

    IMAGE          CREATED        CREATED BY                                       SIZE      COMMENT
    xxxxxxxxxxxx   14 hours ago   CMD ["/bin/sh" "-c" "while :; do sleep 10; d…   0B        buildkit.dockerfile.v0
    <missing>      14 hours ago   RUN |1 PASSWORD=${PASSWORD} /bin/sh -c echo…   4.1kB     buildkit.dockerfile.v0
    <missing>      14 hours ago   ARG PASSWORD=k8s-password                        0B        buildkit.dockerfile.v0
    ...
    ```