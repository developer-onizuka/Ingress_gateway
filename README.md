# Ingress_gateway

This repogitory is a summary of the blog at the URL below:
- https://medium.com/@zhaohuabing/which-one-is-the-right-choice-for-the-ingress-gateway-of-your-service-mesh-21a280d4a29c

We can use kubernetes at the combination between {Kube-Proxy, Sidecar Proxy} and {ClusteIP, NodePort, LoadBalancer, Ingress, Ingress Gateway}.

# 1. Proxy and Services
|  | Layer | Function |
| --- | --- | --- |
| Kube-Proxy | L4 | Kube-proxy creates an iptables rule for each of the backend Pods in the Service. After catching the traffic sent to the ClusterIP, iptables forwards that traffic directly to one of the backend Pod using DNAT. |
| Sidecar Proxy | L7 | A client request is captured and redirected to the sidecar proxy by iptables. Then, the sidecar proxy chooses a backend pod according to the service discovery information and routing rules obtained from the control plane, and forwards the request to it. Istio sidecar proxy works just like Kube-proxy userspace mode. They both work in userspace to proxy the client request and load balance among multiple back-end Pods. The difference is that Kube-proxy only works on OSI layer 4, while Istio sidecar proxy can also handle OSI layer 7 packages.|


# 2. How to expose the service outside
|  | Layer | Function / How to work | Pros | Cons |
| --- | --- | --- | --- | --- |
| ClusterIP | L4 | ClusterIP is only reachable inside a Kubernetes cluster. | A Service is bound to a ClusterIP, which is a virtual IP address. No matter what happens to the backend Pods, the ClusterIP never changes, so a client inside of the cluster can always send requests to the ClusterIP of the Service. | Only provides very basic service discovery and limited load balancing policies. |
| NodePort | L4 | If a Service is declared as NodePort type, Kube-proxy will create a port on the node and listen on that port. But Kube-proxy will not directly accept traffic from node networks, instead, it will create the corresponding iptables rules which will capture the traffic sent to the NodePort and redirect that traffic to the back-end Pods. | NodePort is a convenient tool for testing in your local Kubernetes cluster. | Any node may crash or be removed from a Kubernetes cluster. When a new one comes in, the IP address of the new node is normally dynamically allocated from an address pool, which means we can’t treat node IP as a well-known IP. |
| LoadBalancer | L4 | A service can be declared as LoadBalancer type to create a layer 4 load balancer in front of multiple nodes. As this layer 4 load balancer is outside of the Kubernetes network, a Cloud Provider Controller (Contollers for deploying LoadBalancer) is needed for its provision. This Cloud Provider Controller (Contollers for deploying LoadBalancer) watches the Kubernetes master for the addition and removal of Service resources and configures a layer 4 load balancer in the cloud provider network to proxy the NodePorts on multiple Kubernetes nodes. | The numbers of Nodeports and pods can be scaled out/in accordingly based on the working load of the system. | Kubernetes LoadBalancer works in OSI layer 4, meaning it can only dispatch inbound traffic to the backend services based on the 2-tuple of IP and Port. As a result, if we need to expose multiple services to the outside of a cluster, we must create a LoadBalancer for each service. |
| Ingress | L7 | Kubernetes Ingress resource is used to declare an OSI layer 7 load balancer, which can understand HTTP protocol and dispatch inbound traffic based on the HTTP URL or Host. |   |   |
| Ingress Gateway |   |   |   |   |
