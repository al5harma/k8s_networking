- # Minikube Multi-Node Cluster and Network Testing
- Minikube is designed to create a single-node Kubernetes cluster for local development testing. However, as of version v1.10.0 (released April 2020), minikube supports the creation of multi-node clusters using the `--nodes` flag during cluster creation.
- ## Starting a multi-node cluster
-
  ```
  minikube start --nodes 3
  ```
- This will create a cluster with 3 nodes. Confirm the nodes in your cluster with:
-
  ```
  kubectl get nodes
  ```
- **Note:** Each of these "nodes" is a separate Kubernetes control plane all running on the same physical machine. They're not like nodes in a production Kubernetes cluster. They're really separate clusters that have been configured to look like a multi-node cluster from Kubernetes' point of view.
- ## Deployment Configuration
- In the deployment YAML, specify to create 3 replicas of your pod. This effectively creates 3 pods. However, Kubernetes automatically schedules pods across all available nodes. If using minikube to simulate a multi-node environment, Kubernetes may still schedule multiple pods on the same node if resources allow. To ensure each pod lands on a different node, use a feature called pod anti-affinity.
- ## Setting up a NodePort service
- Ensure that you have a Service of type NodePort set up that targets your pods for access.
- The `requiredDuringSchedulingIgnoredDuringExecution` in your pod anti-affinity rules means that the rule must be met when scheduling the pod. However, if conditions change after the pod is scheduled, the system does not try to reschedule the pod.
- The `labelSelector` looks for pods with a label key of `app` and a value of `pinger`, which is the label of the pods created by this deployment. The `topologyKey` is `kubernetes.io/hostname`, ensuring no two pods with the label `app=pinger` can be scheduled on the same node.
- **Note:** Anti-affinity rules can make it harder for the scheduler to find a suitable place to run a pod, especially in a small cluster. If there are not enough unique nodes for all the pods you're trying to run, some pods may remain unscheduled.
- ## Testing the setup
- Once the local minikube cluster is running 3 nodes, apply the ping-deploy.yml:
-
  ```
  kubectl apply -f ping-deploy.yml
  ```
- Next, get the nodes and pods:
-
  ```
  kubectl get nodes -o wide
  kubectl exec -it <nodename> bash
  ```
- Update the local containers with necessary tools:
-
  ```
  apt-get update
  apt-get install iputils-ping curl dnsutils iproute2 -y
  ip a
  ```
- Next, deploy the web service to front the nodes and create a NodePort service:
-
  ```
  kubectl apply -f ping-deploy.yml
  ```
- Find the minikube node IP:
-
  ```
  minikube ip
  ```
- Then, curl to the NodePort service using the Minikube's IP and the NodePort specified in your service definition:
-
  ```
  curl $(minikube ip):30001
  ```
- This should return the expected response from your service. Remember that the service and the pods it routes to must be running and ready to accept connections. Verify this with:
-
  ```
  kubectl get services
  kubectl get pods
  ```
