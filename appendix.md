# APPENDIX Only Commands

## Prerequisites

```sudo apt-get install docker.io```

```sudo apt-get install minikube```

```sudo apt-get install kubectl```

```npm install -g snyk```

```snyk auth <SNYK_TOKEN>```

## Setup

```minikube start --nodes 3```

```kubectl get nodes```

## Scenario 1

```kubectl apply -f ping-deploy.yml```

```kubectl get nodes -o wide```

```kubectl exec -it <nodename> bash```

```apt-get update```

```apt-get install iputils-ping curl dnsutils iproute2 -y```

```ip a```

## Scenario 2

```kubectl apply -f nginx-deploy.yml```

## Scenario 3

```kubectl apply -f juice-deploy.yml```

```minikube ip```

```curl $(minikube ip):30001```

```kubectl get services```

```kubectl get pods```

## Security Scanning

```snyk iac test <YAML_FILE>```

```snyk container test <IMAGE_NAME>```

## Cleanup

```kubectl delete -f <YAML_FILE>```

```minikube delete```

```docker stop $(docker ps -aq)```

```docker rm $(docker ps -aq)```

```docker rmi $(docker images -q)```

```docker system prune -a```
