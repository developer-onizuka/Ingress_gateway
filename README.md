# Ingress_gateway

|  | Function | Pros | Cons |
| --- | --- | --- | --- |
| ClusterIP | Kube-proxy creates an iptables rule for each of the backend Pods in the Service. After catching the traffic sent to the ClusterIP, iptables forwards that traffic directly to one of the backend Pod using DNAT. Kube-proxy no longer serves as the OSI layer 4 proxy. | No additional Settings needed | Only provides very basic service discovery and limited load balancing policies. |
| NodePort |   |   |   |
| LoadBalancer |   |   |   |
| Ingress |   |   |   |
| Ingress Gateway |   |   |   |
