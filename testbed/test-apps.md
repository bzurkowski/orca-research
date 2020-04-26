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

