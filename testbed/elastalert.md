# Elastalert

## Installation

Add Helm repository:

```bash
$ helm repo add jertel https://helm.jertel.com
```

Create config map with match enhancements:

```
$ kubectl -n logging create cm enhancements \
    --from-file=$HOME/Workspace/orca/orca/helm/examples/integrations/elastalert/enhancements.py
```

Deploy Elastalert cluster using Helm:

```bash
$ helm install elastalert jertel/elastalert2 \
    --version 2.2.2 \
    --namespace logging \
    --create-namespace \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/elastalert/values.yaml \
    --set nodeSelector.role=exp-control
```

Check Elastalert for errors:

```bash
$ kubectl -n logging logs $(kubectl -n logging get pods |grep elastalert |awk '{print $1}') |grep -i error
```

Port-forward Kibana dashboard:

```bash
$ kubectl -n logging port-forward svc/kibana 4444:5601
```

Open [Kibana dashboard](http://localhost:4444) and create index pattern:

![](/assets/images/efk-index-pattern-elastalert.png)

## Cleanup

Delete Elastalert chart release:

```bash
$ helm -n logging delete elastalert
```

Delete config map with match enhancements:

```bash
$ kubectl -n logging delete cm enhancements
```

## Links

* https://www.youtube.com/watch?v=udustJZQ-yI
* https://github.com/jertel/elastalert2
* https://github.com/Yelp/elastalert/issues/288
* https://github.com/jertel/elastalert2/discussions/115
* https://elastalert2.readthedocs.io/en/latest/recipes/faq.html?highlight=flatline#how-can-i-get-a-resolve-event
