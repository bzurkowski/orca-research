# Dashboards

[OpenRCA](http://localhost:8080):

```bash
$ while true; do kubectl -n rca port-forward svc/orca-ui 8080; sleep 0.1; done
```

[Grafana](http://localhost:3000) (user: `admin`, pass: `prom-operator`):

```bash
$ while true; do kubectl -n monitoring port-forward svc/prometheus-grafana 3000:80; sleep 0.1; done
```

[Prometheus](http://localhost:9090):

```bash
$ while true; do kubectl -n monitoring port-forward svc/prometheus-kube-prometheus-prometheus 9090; sleep 0.1; done
```

[Kiali](http://localhost:20001) (user: `admin`, pass: `admin`):

```bash
$ while true; do kubectl -n istio-system port-forward svc/kiali 20001; sleep 0.1; done
```

[Chaos Mesh](http://localhost:2333):

```bash
$ while true; do kubectl -n chaos port-forward svc/chaos-dashboard 2333; sleep 0.1; done
```

[Fortio](http://localhost:8082/fortio):

```bash
$ while true; do kubectl -n load port-forward svc/fortio 8082; sleep 0.1; done
```
