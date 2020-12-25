# 虚拟监听器

1. If you query the listener summary on a pod you will notice Istio generates the following listeners:

   * A listener on `0.0.0.0:15006` that receives all inbound traffic to the pod 
   * a listener on `0.0.0.0:15001` that receives all outbound traffic to the pod, then hands the request over to a virtual listener.
   * A virtual listener per service IP, per each non-HTTP for outbound TCP/HTTPS traffic.
   * A virtual listener on the pod IP for each exposed port for inbound traffic.
   * A virtual listener on `0.0.0.0` per each HTTP port for outbound HTTP traffic.

   ```text
   $ istioctl proxy-config listeners productpage-v1-6c886ff494-7vxhs
   ADDRESS       PORT  MATCH                                            DESTINATION
   10.96.0.10    53    ALL                                              Cluster: outbound|53||kube-dns.kube-system.svc.cluster.local
   0.0.0.0       80    App: HTTP                                        Route: 80
   0.0.0.0       80    ALL                                              PassthroughCluster
   10.100.93.102 443   ALL                                              Cluster: outbound|443||istiod.istio-system.svc.cluster.local
   10.111.121.13 443   ALL                                              Cluster: outbound|443||istio-ingressgateway.istio-system.svc.cluster.local
   10.96.0.1     443   ALL                                              Cluster: outbound|443||kubernetes.default.svc.cluster.local
   10.100.93.102 853   App: HTTP                                        Route: istiod.istio-system.svc.cluster.local:853
   10.100.93.102 853   ALL                                              Cluster: outbound|853||istiod.istio-system.svc.cluster.local
   0.0.0.0       9080  App: HTTP                                        Route: 9080
   0.0.0.0       9080  ALL                                              PassthroughCluster
   0.0.0.0       9090  App: HTTP                                        Route: 9090
   0.0.0.0       9090  ALL                                              PassthroughCluster
   10.96.0.10    9153  App: HTTP                                        Route: kube-dns.kube-system.svc.cluster.local:9153
   10.96.0.10    9153  ALL                                              Cluster: outbound|9153||kube-dns.kube-system.svc.cluster.local
   0.0.0.0       15001 ALL                                              PassthroughCluster
   0.0.0.0       15006 Addr: 10.244.0.22/32:15021                       inbound|15021|mgmt-15021|mgmtCluster
   0.0.0.0       15006 Addr: 10.244.0.22/32:9080                        Inline Route: /*
   0.0.0.0       15006 Trans: tls; App: HTTP TLS; Addr: 0.0.0.0/0       Inline Route: /*
   0.0.0.0       15006 App: HTTP; Addr: 0.0.0.0/0                       Inline Route: /*
   0.0.0.0       15006 App: Istio HTTP Plain; Addr: 10.244.0.22/32:9080 Inline Route: /*
   0.0.0.0       15006 Addr: 0.0.0.0/0                                  InboundPassthroughClusterIpv4
   0.0.0.0       15006 Trans: tls; App: TCP TLS; Addr: 0.0.0.0/0        InboundPassthroughClusterIpv4
   0.0.0.0       15010 App: HTTP                                        Route: 15010
   0.0.0.0       15010 ALL                                              PassthroughCluster
   10.100.93.102 15012 ALL                                              Cluster: outbound|15012||istiod.istio-system.svc.cluster.local
   0.0.0.0       15014 App: HTTP                                        Route: 15014
   0.0.0.0       15014 ALL                                              PassthroughCluster
   0.0.0.0       15021 ALL                                              Inline Route: /healthz/ready*
   10.111.121.13 15021 App: HTTP                                        Route: istio-ingressgateway.istio-system.svc.cluster.local:15021
   10.111.121.13 15021 ALL                                              Cluster: outbound|15021||istio-ingressgateway.istio-system.svc.cluster.local
   0.0.0.0       15090 ALL                                              Inline Route: /stats/prometheus*
   10.111.121.13 15443 ALL                                              Cluster: outbound|15443||istio-ingressgateway.istio-system.svc.cluster.local
   ```

2. From the above summary you can see that every sidecar has a listener bound to `0.0.0.0:15006` which is where IP tables routes all inbound pod traffic to and a listener bound to `0.0.0.0:15001` which is where IP tables routes all outbound pod traffic to. The `0.0.0.0:15001` listener hands the request over to the virtual listener that best matches the original destination of the request, if it can find a matching one. Otherwise, it sends the request to the `PassthroughCluster` which connects to the destination directly.

