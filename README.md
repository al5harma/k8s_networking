Kubernetes Demo Setup with Minikube
This repository provides a demo setup to test multi-node cluster functionalities and network testing in Kubernetes using Minikube. We cover several scenarios, including deploying pods across the cluster with pod anti-affinity, deploying a web application with an Nginx server, and deploying a vulnerable application to demonstrate the importance of security scanning. Both Kubernetes configuration and Docker images are examined for security vulnerabilities using Snyk.

# Minikube Multi-Node Cluster and Network Testing

## Overview
This repository provides a demo setup to test multi-node cluster functionalities and network testing in Kubernetes using Minikube. We cover several scenarios, including deploying pods across the cluster with pod anti-affinity, deploying a web application with an Nginx server, and deploying a vulnerable application to demonstrate the importance of security scanning. Both Kubernetes configuration and Docker images are examined for security vulnerabilities using Snyk.

## Prerequisites
Before you begin, ensure you have the following installed:
- [Docker](https://docs.docker.com/get-docker/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Snyk CLI](https://support.snyk.io/hc/en-us/articles/360003812578-CLI-reference)

Remember to replace `<SNYK_TOKEN>` with your actual Snyk token during Snyk CLI authentication.

## Setup
To begin, start Minikube with 3 nodes:
```minikube start --nodes 3```

Verify the nodes in your cluster with:

```kubectl get nodes```


## Scenarios

### Scenario 1: Ping Pods in Cluster with DaemonSets and ClusterIP

Apply the `ping-deploy.yml` file to deploy the ping testing pod to each node:


```kubectl apply -f ping-deploy.yml```

To view the nodes and pods, use:
```kubectl get nodes -o wide```

To enter the node, use:

```kubectl exec -it <nodename> bash```

Replacing <nodename> with your actual node name. You can then update the local containers with necessary tools:

```apt-get update```
```apt-get install iputils-ping curl dnsutils iproute2 -y```
```ip a```

###Scenario 2: Deploy a Web Application with Nginx
To deploy the web service to front the nodes and create a NodePort service, apply the corresponding yml file:

```kubectl apply -f nginx-deploy.yml```

### Scenario 3: Deploy a Vulnerable Application

Apply the `juice-deploy.yml` file to deploy the Juice Shop application:

```kubectl apply -f juice-deploy.yml```

To find the Minikube node IP, use:

```minikube ip```

Then, you can send a request to the NodePort service using the Minikube's IP and the NodePort specified in your service definition:

```curl $(minikube ip):30001```

This should return the expected response from your service. You can verify this with kubectl get services and kubectl get pods.

## Security Scanning
To scan the Kubernetes configuration files using Snyk IaC, use:

```snyk iac test <YAML_FILE>```

Replacing `<YAML_FILE>` with the name of your YAML file.

To scan Docker images for vulnerabilities, use:

```snyk container test <IMAGE_NAME>```

Replacing `<IMAGE_NAME>` with the name of your Docker image.

## Cleanup

To delete the deployments, use:

```kubectl delete -f <YAML_FILE>```

Replacing `<YAML_FILE>` with the name of your YAML file.

To delete the Minikube cluster, use:

```minikube delete```

(Optional) To stop and remove all Docker containers, use:

```docker stop $(docker ps -aq)
docker rm $(docker ps -aq)```


(Optional) To remove all Docker images, use:

```docker rmi $(docker images -q)```

Be careful as this will remove all Docker images from your system.

(Optional) To prune Docker resources, use:

```docker system prune```

This will remove all stopped containers, dangling images, and unused networks. If you also want to remove all unused volumes, add the -a flag, i.e., docker system prune -a.

By following these steps, you can ensure that your demo environment is cleaned up after your presentation. All deployed applications, pods, and services will be deleted, your local Minikube cluster will be stopped, and optionally, all Docker resources can be cleaned up.
