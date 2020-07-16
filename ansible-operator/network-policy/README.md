# Introduction
Network policies are used to improve your security posture. You can control ingress, egress and restrict communication between pods on need basis.

Here are a few examples that demonstartes how to use network policies. You can use a mix of these to design your own customized and restrictive network policy

## Allow egress
When pods communicate to services outside openshift cluster (either on private network or public network) you can allow selective egress for pods. Please see egress-nwpolicy.yml

## Allow ingress
Ingress rule allows incoming traffic into a pod. Consider, you have a route for a service that directs traffic to pods configured using `selector` in service spec. If you configure a network policy at pod level that does not allow ingres, the pod will not receive any traffic. You may want to use ingress to allow traffic only from selective networks. Please see ingress-nwpolicy.yml

## Restrict communication between pods
You can allow / restrict traffic between pods using a specific network policy for each pod. Please see pod-comm-nwpolicy.yml. This policy allows traffic from pods with label my-app-2 and my-app-3 communicate with pods with label my-app.

If you use prometheus for monitoring, this also allows prometheus pod to communicate with your pod for scrapping metrics.