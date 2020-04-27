# Test Applications

## Hipster Shop

Clone app repository:

```bash
$ git clone https://github.com/GoogleCloudPlatform/microservices-demo.git
```

Create namespace:

```bash
$ kubectl create namespace hipster-shop
$ kubectl label namespace hipster-shop istio-injection=enabled
```

Apply Kubernetes manifests:

```bash
$ kubectl -n hipster-shop apply -f ./release/kubernetes-manifests.yaml
```

Apply Istio manifests:

```bash
$ kubectl -n hipster-shop apply -f ./release/istio-manifests.yaml
```

## Bookinfo

Clone Istio repository:

```bash
$ git clone https://github.com/istio/istio.git
$ cd ./istio
$ git checkout -b v1.4.5 tags/1.4.5
```

Create namespace:

```bash
$ kubectl create namespace bookinfo
$ kubectl label namespace bookinfo istio-injection=enabled
```

Apply Kubernetes manifests:

```bash
$ kubectl -n bookinfo apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

Verify if the application is running:

```bash
$ kubectl -n bookinfo exec -it \
    $(kubectl -n bookinfo get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}') \
    -c ratings \
    -- curl productpage:9080/productpage | grep -o "<title>.*</title>"
```

Apply gateway configuration:

```bash
$ kubectl -n bookinfo apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```

Apply initial destination rules:

```bash
$ kubectl -n bookinfo apply -f samples/bookinfo/networking/destination-rule-all.yaml
```

Apply initial routing rules:

```bash
$ kubectl -n bookinfo apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```

Generate load via the Ingress Gateway:

```bash
ingress_host=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.clusterIP}')
while true; do curl -I http://$ingress_host/productpage; sleep 0.1; done
```

## Isotope

Prerequisite: Go lang installed in the system.

Clone Istio tools:

```bash
$ git clone https://github.com/istio/tools.git
```

Install `graphviz`:

```bash
$ yum install -y graphviz
```

Read usage instructions:

```bash
$ cd ./tools/isotope/convert/
$ go run main.go -h
```

```
Converts a service graph expressed in YAML to various formats

Usage:
  service-grapher [command]

Available Commands:
  graphviz    Convert a .yaml file to a Graphviz DOT language file
  help        Help about any command
  kubernetes  Convert service graph YAML to manifests for performance testing

Flags:
  -h, --help   help for service-grapher

Use "service-grapher [command] --help" for more information about a command.
```

Explore sample mesh topologies:

```
$ ls -l .tools/isotope/example-topologies/
total 92
-rw-r--r--. 1 root root    92 Apr 27 08:47 1-service.yaml
-rw-r--r--. 1 root root   406 Apr 27 08:47 10-svc_10-end.yaml
-rw-r--r--. 1 root root   857 Apr 27 08:47 10-svc_100-end.yaml
-rw-r--r--. 1 root root   408 Apr 27 08:47 10-svc_1000-end.yaml
-rw-r--r--. 1 root root   409 Apr 27 08:47 10-svc_10000-end.yaml
-rw-r--r--. 1 root root 41530 Apr 27 08:47 1000-svc_5000-end.yaml
-rw-r--r--. 1 root root   223 Apr 27 08:47 canonical-2-replicas.yaml
-rw-r--r--. 1 root root   227 Apr 27 08:47 canonical.yaml
-rw-r--r--. 1 root root   124 Apr 27 08:47 chain-2-services.yaml
-rw-r--r--. 1 root root   105 Apr 27 08:47 chain-3-services.yaml
-rw-r--r--. 1 root root  4581 Apr 27 08:47 tree-111-services.yaml
-rw-r--r--. 1 root root   576 Apr 27 08:47 tree-13-services.yaml
```

Generate mesh graph:

```bash
$ cd ./tools/isotope/convert
$ go run main.go graphviz ../example-topologies/tree-13-services.yaml gen-mesh.dot
$ dot -Tpng gen-mesh.dot -o gen-mesh.png
$ ls -l |grep gen-mesh
gen-mesh.dot  gen-mesh.png
```

Generate Kubernetes manifests:

```bash
$ go run main.go kubernetes ../example-topologies/tree-13-services.yaml
    --client-image=tahler/fortio:prometheus \
    --service-image=tahler/isotope-service:1 \
    --service-node-selector='isotope=service-pool' \
    --client-node-selector='isotope=client-pool' \
    --service-max-idle-connections-per-host=64 > gen-mesh.yaml
```
