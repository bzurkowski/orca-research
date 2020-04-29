# RCA based on graph operations (PoC)

## Operations

Identify unreachable nodes and emit deduced alerts (`PodUnreachable`) on connected pods:

```
FOR node IN nodes
    FILTER node.kind == "node"
    FILTER node.deleted_at == null
    LET node_unreachable = FIRST(
        FOR node_alert, link IN 1..1 ANY node links
            FILTER node_alert.kind == "alert"
            FILTER node_alert.deleted_at == null
            FILTER node_alert.properties.name == "KubeNodeUnreachable"
            RETURN node_alert
        )
    FILTER node_unreachable != null
    FOR pod, link IN 1..1 ANY node links
        FILTER pod.kind == "pod"
        FILTER pod.deleted_at == null
        INSERT {origin: "openrca", kind: "alert", properties: {name: "PodUnreachable"}} INTO nodes
        LET pod_alert = NEW
        INSERT {origin: "openrca", _from: pod_alert._id, _to: pod._id } INTO links
```

Identify unreachable pods and emit deduced alerts (`ServiceDegraded`) on connected services:

```
FOR svc IN nodes
    FILTER svc.kind == "service"
    FILTER svc.deleted_at == null
    LET svc_pods = (
        FOR pod, link IN 1..1 ANY svc links
            FILTER pod.kind == "pod"
            FILTER pod.deleted_at == null
            RETURN pod
    )
    LET svc_unreachable_pod = FIRST(
        FOR pod IN svc_pods
            FOR pod_alert, link IN 1..1 ANY pod links
                FILTER pod_alert.kind == "alert"
                FILTER pod_alert.deleted_at == null
                FILTER pod_alert.properties.name == "PodUnreachable"
                RETURN pod_alert
    )
    FILTER svc_unreachable_pod != null
    INSERT {origin: "openrca", kind: "alert", properties: {name: "ServiceDegraded"}} INTO nodes
    LET svc_alert = NEW
    INSERT {origin: "openrca", _from: svc_alert._id, _to: svc._id } INTO links
```

Identify unreachable pods and emit deduced alerts (`ReplicaSetDegraded`) on connected replica sets:

```
FOR rs IN nodes
    FILTER rs.kind == "replica_set"
    FILTER rs.deleted_at == null
    LET rs_pods = (
        FOR pod, link IN 1..1 ANY rs links
            FILTER pod.kind == "pod"
            FILTER pod.deleted_at == null
            RETURN pod
    )
    LET rs_unreachable_pod = FIRST(
        FOR pod IN rs_pods
            FOR pod_alert, link IN 1..1 ANY pod links
                FILTER pod_alert.kind == "alert"
                FILTER pod_alert.deleted_at == null
                FILTER pod_alert.properties.name == "PodUnreachable"
                RETURN pod_alert
    )
    FILTER rs_unreachable_pod != null
    INSERT {origin: "openrca", kind: "alert", properties: {name: "ReplicaSetDegraded"}} INTO nodes
    LET rs_alert = NEW
    INSERT {origin: "openrca", _from: rs_alert._id, _to: rs._id } INTO links
```

Construct alerts graph:

```
FOR link IN links
    LET source = DOCUMENT(link._from)
    LET target = DOCUMENT(link._to)
    FILTER source.deleted_at == null
    FILTER target.deleted_at == null
    LET source_alert = FIRST(
        FOR n, l IN 1..1 ANY source links
            FILTER n.deleted_at == null
            FILTER n.kind == "alert"
            RETURN n
    )
    LET target_alert = FIRST(
        FOR n, l IN 1..1 ANY target links
            FILTER n.deleted_at == null
            FILTER n.kind == "alert"
            RETURN n
    )
    FILTER source_alert != null AND target_alert != null
    INSERT {origin: "openrca", _from: source_alert._id, _to: target_alert._id} INTO links
```

Traverse alerts graph:

```
FOR link IN links
    FILTER link.origin == "openrca"
    RETURN link
```

## Cheatsheet

Remove all RCA nodes:

```
FOR node IN nodes
    FILTER node.origin == "openrca"
    REMOVE node IN nodes
```

Remove all RCA links:

```
FOR link IN links
    FILTER link.origin == "openrca"
    REMOVE link IN links
```
