FROM ubuntu:22.04

RUN useradd -m -U -u 1000 k8s-user

USER k8s-user

WORKDIR /home/k8s-user

CMD ["/bin/sh", "-c", "while :; do sleep 10; done"]
