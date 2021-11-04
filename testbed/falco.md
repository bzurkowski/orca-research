# Falco

## Installation

Add required flags to the API Server manifest:

```bash
# /etc/kubernetes/manifests/kube-apiserver.yaml
...
--audit-dynamic-configuration
--feature-gates=DynamicAuditing=true
--runtime-config=auditregistration.k8s.io/v1alpha1=true
```

After saving the file, the API server should be restarted automatically with audit log enabled.

Deploy Falco chart:

```bash
$ helm install stable/falco \
    --name falco \
    --namespace falco \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/falco/values.yaml
```

By default, Falco chart creates an audit sink policy with `service` webhook reference instead of `url` reference. It is correct for in-cluster webhook setup, however, for some reason, when using `service` reference, the specified webhook port is not respected by the API server - it attempts to communicate with the webhook service using HTTPS on port 443. To fix the issue, the created audit sink must be modified to use `url` reference for which the port is respected:

```bash
$ kubectl edit auditsink falco
```

```yaml
apiVersion: auditregistration.k8s.io/v1alpha1
kind: AuditSink
metadata:
  creationTimestamp: "2020-01-24T15:50:01Z"
  generation: 3
  name: falco
  resourceVersion: "13417243"
  selfLink: /apis/auditregistration.k8s.io/v1alpha1/auditsinks/falco
  uid: 0741fd30-4bd6-4da6-8490-24717670a083
spec:
  policy:
    level: RequestResponse
    stages:
    - ResponseComplete
    - ResponseStarted
  webhook:
    clientConfig:
      url: http://<falco-svc-cluster-ip>:8765/k8s-audit
    throttle:
      burst: 15
      qps: 10
```
