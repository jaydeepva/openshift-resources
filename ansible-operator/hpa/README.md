# Introduction
You can use horizontal pod autoscaler to scale your deployment either based on cpu/memory or your own custom metrics. However when you use HPA with operator, there is a conflict on who controls the number of replicas. HPA may try to scale up OR scale down number of replicas based on defined rules, the operator will reset it back to what's defined in the deployment definition.

To solve this problem, you have two options
- Let operator delegate replicas management to HPA
- Do not use HPA and include logic in operator to control number of replicas

## Replica management by HPA
In this scenario, operator is used to create initial deployment and HPA policy. Once created, operator will not watch the deployment and let HPA manage scaling up and down of the number of replicas.

In `main.yml` you can see that the task creates deployment if it does not exists. It also creates HPA to manage scaling. When reconciliation loop runs, now, the operator will not reconcile deployment state with defined state.
