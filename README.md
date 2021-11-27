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
| ClusterIP | L4 | A Service is bound to a ClusterIP, which is a virtual IP address. | No matter what happens to the backend Pods, the ClusterIP never changes, so a client inside of the cluster can always send requests to the ClusterIP of the Service. | Only provides very basic service discovery and limited load balancing policies. |
| NodePort |   |   |   |   |
| LoadBalancer |   |   |   |   |
| Ingress |   |   |   |   |
| Ingress Gateway |   |   |   |   |
