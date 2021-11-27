# Ingress_gateway

This repogitory is a summary of the blog at the URL below:
- https://medium.com/@zhaohuabing/which-one-is-the-right-choice-for-the-ingress-gateway-of-your-service-mesh-21a280d4a29c

We can use kubernetes at the combination among {Kube-Proxy, Sidecar Proxy}, {ClusteIP, NodePort, LoadBalancer} and {Ingress, Ingress Gateway, API Gateway}.

# 1. Proxy
|  | Layer | Function |
| --- | --- | --- |
| Kube-Proxy | L4 | Kube-proxy creates an iptables rule for each of the backend Pods in the Service. After catching the traffic sent to the ClusterIP, iptables forwards that traffic directly to one of the backend Pod using DNAT. |
| Sidecar Proxy | L7 | A client request is captured and redirected to the sidecar proxy by iptables. Then, the sidecar proxy chooses a backend pod according to the service discovery information and routing rules obtained from the control plane, and forwards the request to it. Istio sidecar proxy works just like Kube-proxy userspace mode. They both work in userspace to proxy the client request and load balance among multiple back-end Pods. The difference is that Kube-proxy only works on OSI layer 4, while Istio sidecar proxy can also handle OSI layer 7 packages.|


# 2. How to expose the service outside at L4
|  | Layer | Function / How to work | Pros | Cons |
| --- | --- | --- | --- | --- |
| ClusterIP | L4 | ClusterIP is only reachable inside a Kubernetes cluster. | A Service is bound to a ClusterIP, which is a virtual IP address. No matter what happens to the backend Pods, the ClusterIP never changes, so a client inside of the cluster can always send requests to the ClusterIP of the Service. | Only provides very basic service discovery and limited load balancing policies. |
| NodePort | L4 | If a Service is declared as NodePort type, Kube-proxy will create a port on the node and listen on that port. But Kube-proxy will not directly accept traffic from node networks, instead, it will create the corresponding iptables rules which will capture the traffic sent to the NodePort and redirect that traffic to the back-end Pods. | NodePort is a convenient tool for testing in your local Kubernetes cluster. | Any node may crash or be removed from a Kubernetes cluster. When a new one comes in, the IP address of the new node is normally dynamically allocated from an address pool, which means we can’t treat node IP as a well-known IP. |
| LoadBalancer | L4 | A service can be declared as LoadBalancer type to create a layer 4 load balancer in front of multiple nodes. As this layer 4 load balancer is outside of the Kubernetes network, a Cloud Provider Controller (Contollers for deploying LoadBalancer) is needed for its provision. This Cloud Provider Controller (Contollers for deploying LoadBalancer) watches the Kubernetes master for the addition and removal of Service resources and configures a layer 4 load balancer in the cloud provider network to proxy the NodePorts on multiple Kubernetes nodes. | The numbers of Nodeports and pods can be scaled out/in accordingly based on the working load of the system. | Kubernetes LoadBalancer works in OSI layer 4, meaning it can only dispatch inbound traffic to the backend services based on the 2-tuple of IP and Port. As a result, if we need to expose multiple services to the outside of a cluster, we must create a LoadBalancer for each service. |

# 3. How to expose in addition to L4
|  | Layer | Function / How to work | Pros | Cons |
| --- | --- | --- | --- | --- |
| Ingress | L7 | Kubernetes Ingress resource is used to declare an OSI layer 7 load balancer, which can understand HTTP protocol and dispatch inbound traffic based on the HTTP URL or Host. In order for the Ingress resource to work, the cluster must have an ingress controller running. Ingress controllers configure a layer 7 proxy to fulfil the ingress rules. Ingress controller must work together with NodePort and LoadBalancer to provide the full path for the external traffic to enter the cluster. | Each of the NodePort, Ingress or Pod layers can be scale out/in accordingly to handle different working loads. | Kubernetes Ingress can only provide very basic layer 7 capabilities. It doesn’t have the same functionalities as mesh sidecars including advanced routing rules, distributed tracing, policy checking and metrics collections. |
| Ingress Gateway | L4-L6 | Load Balancing SSL termination Virtual hosting. Advanced traffic routing fault injection, etc. | It can provide the same capabilities at the entrance of the mesh as inside the mesh. Both the ingress gateway and the sidecar proxies are managed by a unified mesh control plane. The same resource used for routing configuration inside the mesh. | It can only configure L4-L6 functions, such as port, host, TLS key and certification. |
| API Gateway | L7 | Load Balancing SSL termination Virtual hosting. Traffic routing, API lifecycle management, API Operation Monitoring, API key management, API transformation and API Billing and Rate limiting. | There’s a dozen of API Gateways on the table, including Ambassador, Kong, Traefik, Gloo, etc. | All these API Gateways can be used as a Kubernetes ingress controller, but they all add some kinds of extensions to try to fill the gap between Kubernetes ingress and the reality. |
