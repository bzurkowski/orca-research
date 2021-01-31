# Dashboards

[OpenRCA](http://localhost:8080):

```bash
$ kubectl -n rca port-forward svc/orca-ui 8080
```

[Grafana](http://localhost:3000) (user: `admin`, pass: `prom-operator`):

```bash
$ kubectl -n monitoring port-forward svc/prometheus-grafana 3000:80
```

[Prometheus](http://localhost:9090):

```bash
$ kubectl -n monitoring port-forward svc/prometheus-kube-prometheus-prometheus 9090
```

[Kiali](http://localhost:20001) (user: `admin`, pass: `admin`):

```bash
$ kubectl -n istio-system port-forward svc/kiali 20001
```

[Chaos Mesh](http://localhost:2333):

```bash
$ kubectl -n chaos port-forward svc/chaos-dashboard 2333
```

[Fortio](http://localhost:8080/fortio):

```bash
$ kubectl -n default port-forward svc/client 8080
```
