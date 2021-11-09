# Elastalert

## Installation

Add Helm repository:

```bash
$ helm repo add jertel https://helm.jertel.com
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

## Cleanup

Delete Elastalert chart release:

```bash
$ helm -n logging delete elastalert
```

## Links

* https://www.youtube.com/watch?v=udustJZQ-yI
* https://github.com/jertel/elastalert2
* https://github.com/Yelp/elastalert/issues/288
* https://github.com/jertel/elastalert2/discussions/115
* https://elastalert2.readthedocs.io/en/latest/recipes/faq.html?highlight=flatline#how-can-i-get-a-resolve-event
