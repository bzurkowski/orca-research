# Elastalert

## Installation

Deploy Elastalert chart:

```bash
$ helm install stable/elastalert \
    --name elastalert \
    --namespace logging \
    --values $HOME/orca/helm/examples/integrations/elastalert/orca-values.yaml
```

## Cleanup

Delete Elastalert chart release:

```bash
$ helm delete --purge elastalert
```

## Links

* https://www.youtube.com/watch?v=udustJZQ-yI
