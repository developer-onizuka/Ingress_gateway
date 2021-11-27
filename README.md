# Ingress_gateway

This repogitory is a summary of the blog at the URL below:
- https://medium.com/@zhaohuabing/which-one-is-the-right-choice-for-the-ingress-gateway-of-your-service-mesh-21a280d4a29c

# 1. Proxy and Services
|  | Layer | Function |
| --- | --- | --- |
| Kube-Proxy | L4 | Kube-proxy creates an iptables rule for each of the backend Pods in the Service. After catching the traffic sent to the ClusterIP, iptables forwards that traffic directly to one of the backend Pod using DNAT. |
| Istio Sidecar Proxy | L7 | A client request is captured and redirected to the sidecar proxy by iptables. Then, the sidecar proxy chooses a backend pod according to the service discovery information and routing rules obtained from the control plane, and forwards the request to it. Istio sidecar proxy works just like Kube-proxy userspace mode. They both work in userspace to proxy the client request and load balance among multiple back-end Pods. The difference is that Kube-proxy only works on OSI layer 4, while Istio sidecar proxy can also handle OSI layer 7 packages.|


# 2. How to expose the service outside
|  | Layer | Function | Pros | Cons |
| --- | --- | --- | --- | --- |
| ClusterIP | L4 | ClusterIP is only reachable inside a Kubernetes cluster. | A Service is bound to a ClusterIP, which is a virtual IP address. No matter what happens to the backend Pods, the ClusterIP never changes, so a client inside of the cluster can always send requests to the ClusterIP of the Service. | Only provides very basic service discovery and limited load balancing policies. |
| NodePort | L4 | If a Service is declared as NodePort type, Kube-proxy will create a port on the node and listen on that port. But Kube-proxy will not directly accept traffic from node networks, instead, it will create the corresponding iptables rules which will capture the traffic sent to the NodePort and redirect that traffic to the back-end Pods. | NodePort is a convenient tool for testing in your local Kubernetes cluster. | Any node may crash or be removed from a Kubernetes cluster. When a new one comes in, the IP address of the new node is normally dynamically allocated from an address pool, which means we can’t treat node IP as a well-known IP. |
| LoadBalancer |   |   |   |   |
| Ingress |   |   |   |   |
| Ingress Gateway |   |   |   |   |
