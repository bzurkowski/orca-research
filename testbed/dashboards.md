# Dashboards

[OpenRCA](http://localhost:8080):

```bash
$ kubectl -n rca port-forward svc/orca-ui 8080
```

[Grafana](http://localhost:3000):

```bash
$ kubectl -n monitoring port-forward svc/prometheus-grafana 3000:80
```

[Prometheus](http://localhost:9090):

```bash
$ kubectl -n monitoring port-forward svc/prometheus-kube-prometheus-prometheus 9090
```

[Kiali](http://localhost:20001):

```bash
$ kubectl -n istio-system port-forward svc/kiali 2001
```

[Chaos Mesh](http://localhost:2333):

```bash
$ kubectl -n chaos port-forward svc/chaos-dashboard 2333
```
