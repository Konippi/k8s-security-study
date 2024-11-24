FROM ubuntu:22.04

RUN --mount=type=secret,id=password echo "Password: $(cat /run/secrets/password)"

CMD ["/bin/sh", "-c", "while :; do sleep 10; done"]
