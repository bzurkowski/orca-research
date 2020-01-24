# Falco

## Rules

Default syscall rules:
https://github.com/falcosecurity/falco/blob/dev/rules/falco_rules.yaml

Default K8S audit rules:
https://github.com/falcosecurity/falco/blob/dev/rules/k8s_audit_rules.yaml

Custom rules for application profiles (Nginx, MongoDB, Redis, K8S API Server etc.):
https://github.com/falcosecurity/falco/blob/dev/rules/application_rules.yaml
https://github.com/falcosecurity/profiles

## Generating sample events

https://falco.org/docs/event-sources/sample-events/

```bash
    $ docker pull sysdig/falco-event-generator
    $ docker run -it --name falco-event-generator sysdig/falco-event-generator [syscall|k8s_audit (<rule name>|all)|bash]
```

For K8S audit log:

```bash
    $ docker run -v $HOME/.kube:/root/.kube -it falcosecurity/falco-event-generator k8s_audit
```

