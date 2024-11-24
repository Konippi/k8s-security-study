# Use build secret in container image

This is an example of how to use a build secret in a container image.

## Steps

1. Set an environment variable to enable Docker BuildKit.

    ```bash
    > export DOCKER_BUILDKIT=1
    ```

2. Build the container image.

    ```bash
    > docker build --progress=plain --secret id=password,src=PASSWORD -t build-secret-container-image .

    ...
    #6 [stage-0 2/2] RUN --mount=type=secret,id=password echo "Password: $(cat /run/secrets/password)"
    #6 0.227 Password: confidential-password # PASSWORD is printed
    #6 DONE 0.3s
    ...
    ```

3. Check to see if the container does not contain secret.

    ```bash
    > docker run --rm build-secret-container-image cat /run/secrets/password
    
    cat: can't open '/run/secrets/password': No such file or directory
    ```