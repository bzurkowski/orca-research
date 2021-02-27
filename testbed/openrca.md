# OpenRCA

## Installation

Deploy OpenRCA chart:

```bash
$ helm install orca $HOME/Workspace/orca/orca/helm/orca \
    --namespace rca \
    --create-namespace \
    --set image.pullPolicy=Always \
    --set image.tag=latest \
    --set ui.image.pullPolicy=Always \
    --set ui.image.tag=latest \
    --set probes.prometheus.enabled=true \
    --set probes.prometheus.url=http://prometheus-kube-prometheus-prometheus.monitoring:9090 \
    --set probes.kiali.enabled=true \
    --set probes.kiali.url=http://kiali.istio-system:20001 \
    --set probes.kiali.username=admin \
    --set probes.kiali.password=admin \
    --set ingestors.prometheus.enabled=true \
    --set ingestors.falco.enabled=true \
    --set nodeSelector."kubernetes\.io/hostname"=node3 \
    --set ui.nodeSelector."kubernetes\.io/hostname"=node3 \
    --set arangodb.nodeSelector."kubernetes\.io/hostname"=node3
```

## Cleanup

Delete OpenRCA chart release:

```bash
$ helm delete --purge orca
```
