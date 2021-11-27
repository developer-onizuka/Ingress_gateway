# Ingress_gateway

This repogitory is a summary of the blog at the URL below:
- https://medium.com/@zhaohuabing/which-one-is-the-right-choice-for-the-ingress-gateway-of-your-service-mesh-21a280d4a29c

|  | Layer | Function | Pros | Cons |
| --- | --- | --- | --- | --- |
| ClusterIP | L4 | A Service is bound to a ClusterIP, which is a virtual IP address. | No matter what happens to the backend Pods, the ClusterIP never changes, so a client inside of the cluster can always send requests to the ClusterIP of the Service. | Only provides very basic service discovery and limited load balancing policies. |
| NodePort |   |   |   |   |
| LoadBalancer |   |   |   |   |
| Ingress |   |   |   |   |
| Ingress Gateway |   |   |   |   |


|  | Layer | Function | Pros | Cons |
| --- | --- | --- | --- | --- |
| Kube-Proxy | UserSpace | Kube-proxy creates an iptables rule for each of the backend Pods in the Service. | --- | --- |
| iptables (Service) | Kernel | After catching the traffic sent to the ClusterIP, iptables forwards that traffic directly to one of the backend Pod using DNAT. | --- |

|  | Layer | Function | Pros | Cons |
| --- | --- | --- | --- | --- |
| Istio Sidecar Proxy | UserSpace | --- | --- | --- |
