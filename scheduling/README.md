# Introduction
OpenShift provides control to you how you want to schedule or place the pods onto different nodes. Two common scheduling approaches used are node affinity and pod anti affinity

## node-affinity
Node affinity allows a pod to specify an affinity towards a group of nodes it can be placed on. For example, you could configure a pod to only run on a node with a specific CPU or in a specific availability zone.

One common example is if you are supporting x86 arch, you would want to place pods only on nodes with supported architecture.

The node affinity scheduling defined in `node-affinity-deployment.yml` states that
- the node selection is mandatory during scheduling indicated by `requiredDuringSchedulingIgnoredDuringExecution`. If NO nodes found matching criteria, pods will not be scheduled
- Key `kubernetes.io/arch` and value `amd64` selects nodes that has `amd64` arch. `amd64` value is automatically assigned to nodes by OpenShift when OpenShift cluster is deployed or nodes are added to cluster.

## pod-anti-affinity
Pod affinity and pod anti-affinity allow you to specify rules about how pods should be placed relative to other pods.

For example, using affinity rules, you could spread or pack pods within a service or relative to pods in other services. Anti-affinity rules allow you to prevent pods of a particular service from scheduling on the same nodes as pods of another service that are known to interfere with the performance of the pods of the first service. Or, you could spread the pods of a service across nodes or availability zones to reduce correlated failures.

One common example is spreading the pods over the available nodes to reduce impact of node failures.

The pod anti affinity scheduling defined in `pod-anti-affinity-deployment.yml` states that
- the scheduling is preferred indicated by `preferredDuringSchedulingIgnoredDuringExecution`, but if pods cannot be scheduled then the policy will just be ignored
- the pods are labelled with key `name` and value `my-app`. The selector selects pods with these labels and applies anti afinity policy
- the number of replicas should be more than 2 to effect this policy
