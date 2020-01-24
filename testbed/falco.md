# Falco

## Installation

### Enable dynamic audit log backend

Add the following flags to the API Server manifest (`/etc/kubernetes/manifests/kube-apiserver.yaml`):

```bash
    --audit-dynamic-configuration
    --feature-gates=DynamicAuditing=true
    --runtime-config=auditregistration.k8s.io/v1alpha1=true
```

https://kubernetes.io/docs/tasks/debug-application-cluster/audit/#dynamic-backend
https://github.com/falcosecurity/falco/blob/dev/examples/k8s_audit_config/enable-k8s-audit.sh
https://github.com/falcosecurity/falco/tree/dev/examples/k8s_audit_config#instructions-for-kubernetes-113

### Deploy Helm chart

```bash
    $ helm install stable/falco \
        --name falco \
        --namespace falco \
        --set auditLog.enabled=true \
        --set auditLog.dynamicBackend.enabled=true \
        --set falco.jsonOutput=true \
        --set falco.httpOutput.enabled=true \
        --set falco.httpOutput.url=http://orca.rca:5000/v1/ingestor/falco \
        --set resources.requests.cpu=100m \
        --set resources.requests.memory=100Mi \
        --set resources.limits.cpu=1000m \
        --set resources.limits.memory=1000Mi
```

### Fix webhook backend reference in Audit Sink policy

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
