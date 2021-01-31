# OpenRCA

## Installation

Deploy OpenRCA chart:

```bash
$ helm install $HOME/Workspace/orca/orca/helm/orca \
    --name orca \
    --namespace rca \
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
    --set nodeSelector."kubernetes\.io/hostname"=node2 \
    --set ui.nodeSelector."kubernetes\.io/hostname"=node2 \
    --set arangodb.nodeSelector."kubernetes\.io/hostname"=node2
```

## Cleanup

Delete OpenRCA chart release:

```bash
$ helm delete --purge orca
```
