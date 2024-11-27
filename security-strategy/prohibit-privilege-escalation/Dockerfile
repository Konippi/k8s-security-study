FROM ubuntu:22.04

RUN apt update && \
    apt install -y sudo && \
    useradd -m -U -u 1000 k8s-user && \
    groupadd wheel && \
    usermod -aG wheel k8s-user && \
    echo "%wheel ALL=NOPASSWD: ALL" >> /etc/sudoers && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*

USER k8s-user

WORKDIR /home/k8s-user

CMD ["/bin/sh", "-c", "while :; do sleep 10; done"]
