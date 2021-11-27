# Ingress_gateway

This repogitory is a summary of the blog at the URL below:
- https://medium.com/@zhaohuabing/which-one-is-the-right-choice-for-the-ingress-gateway-of-your-service-mesh-21a280d4a29c

|  | Function | Pros | Cons |
| --- | --- | --- | --- |
| ClusterIP | Kube-proxy creates an iptables rule for each of the backend Pods in the Service. After catching the traffic sent to the ClusterIP, iptables forwards that traffic directly to one of the backend Pod using DNAT. Kube-proxy no longer serves as the OSI layer 4 proxy. | No additional Settings are needed | Only provides very basic service discovery and limited load balancing policies. |
| NodePort |   |   |   |
| LoadBalancer |   |   |   |
| Ingress |   |   |   |
| Ingress Gateway |   |   |   |
