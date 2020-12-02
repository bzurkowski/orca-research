# OpenRCA

Install using Helm:

```bash
$ helm install ./helm/orca \
    --name orca \
    --namespace rca \
    --set image.pullPolicy=Always \
    --set probes.prometheus.enabled=true \
    --set probes.prometheus.url=http://prometheus-prometheus-oper-prometheus.monitoring:9090 \
    --set ingestors.prometheus.enabled=true \
    --set ingestors.falco.enabled=true \
    --set probes.kiali.enabled=true \
    --set probes.kiali.url=http://kiali.istio-system:20001 \
    --set probes.kiali.username=admin \
    --set probes.kiali.password=admin \
    --set nodeSelector."kubernetes\.io/hostname"=node2 \
    --set arangodb.nodeSelector."kubernetes\.io/hostname"=node2
```
