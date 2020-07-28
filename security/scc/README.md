# Introduction
Security context constraints allow administrators to control permissions for pods. This folder contains various resources to help you with your scc configurations

## Service account and SCC
It is recommended to use dedicated service account so that you can assign only minimal required permissions to your containers. You should also assign image pull secret to your service account so that OpenShift will be able to pull images when using specified service account.

Sometimes your containers are good to run with restricted SCC (Note - `restricted` SCC is provided by OpenShift OOB), still you should define and use a dedicated service account

The trick is to define service account and link it to your deployment. Do not assign any SCC to the service account. OpenShift by default then assigns `restricted` SCC to your container. In future, you can create your custom SCC and link your service account with that. No changes will be required in your container.

Ensure that any securityContext defined in your deployment does not conflict with restricted SCC. If you specify userid in `runAsUser`, the assigned SCC will be `anyuid`.

See `my-deployment.yaml` and `my-service-account.yaml`
