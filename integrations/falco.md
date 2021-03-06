# Falco

## Rules

Default syscall rules:
* https://github.com/falcosecurity/falco/blob/dev/rules/falco_rules.yaml

Default K8S audit rules:
* https://github.com/falcosecurity/falco/blob/dev/rules/k8s_audit_rules.yaml

Custom rules for application profiles (Nginx, MongoDB, Redis, K8S API Server etc.):
* https://github.com/falcosecurity/falco/blob/dev/rules/application_rules.yaml
* https://github.com/falcosecurity/profiles

Audit payloads:
* https://github.com/falcosecurity/falco/tree/master/test/trace_files/k8s_audit

## Generating events

Falco provides scripts to generate the activity matching the syscall and k8s event rulesets:

  ```bash
  $ docker pull sysdig/falco-event-generator
  $ docker run -it --name falco-event-generator sysdig/falco-event-generator [syscall|k8s_audit (<rule name>|all)|bash]
  ```

For K8S audit log:

  ```bash
  $ docker run -v $HOME/.kube:/root/.kube -it falcosecurity/falco-event-generator k8s_audit
  ```

* https://falco.org/docs/event-sources/sample-events/
