![Logo](https://github.com/raftechio/.github/blob/6f694eb425c2ec275e41a7677941b8f8d957b2b5/github-repo-logo.jpeg)

# cnoe-stacks-custom
We use `idpbuilder` to create the stack. Please refer to the [idpbuilder](https://github.com/cnoe-io/idpbuilder) repository for more information.

Let me start of that idpbuilder is a tool that ever engineer should have in its toolbox! In this repository you will find a set of custom stacks for idpbuilder that we use on regular basis to explore various scenarios or enable our customers to test new features. 

> Some of the settings are not really `prod` like or opinionated so please check the values files and/or adjust as needed!

## Using the custom stacks

Since some of the referenced stacks are using OCI repositories we need to provide a custom `argocd-cm.yaml` file. You can find it in the `idpbuilder-overrides` folder.

| Stack Name       | Description                                                             |
| ---------------- | ----------------------------------------------------------------------- |
| bitnami-mysql    | Bitnami MySQL deployment configured for TeamCity                        |
| kro              | Kubernetes Resource Observer - monitoring tool for Kubernetes resources |
| nfs              | NFS Server Provisioner for dynamic storage provisioning                 |
| prometheus-stack | Prometheus monitoring stack with Grafana dashboards                     |
| grafana-operator | Grafana Operator for managing Grafana instances                         |


## Examples: 

> Example 1 - using remote references

```bash
idpbuilder create \
  -p https://github.com/raftechio/cnoe-stacks-custom/stack/kro \
  -c argocd:./idpbuilder-overrides/argocd-cm.yaml \
```

> Example 2 - using local references of all of the packages and custom host

```bash
idpbuilder create --name raftech \
  -p stack/bitnami-mysql \
  -p stack/prometheus-stack \
  -p stack/kro \
  -p stack/nfs \
  -c argocd:./idpbuilder-overrides/argocd-cm.yaml \
  --kind-config=https://raw.githubusercontent.com/raftechio/cnoe-stacks-custom/refs/heads/main/kind-config/cluster-with-nfs.yml \
  --host raftech.localtest.me \
  --dev-password
```

> Example 3 - using local references of all of the packages and custom host

```bash
idpbuilder create --name raftech \
  -p stack/prometheus-stack \
  -p stack/cert-manager \
  -p stack/argo-rollouts \
  -p stack/kargo \
  -c argocd:./idpbuilder-overrides/argocd-cm.yaml \
  --kind-config=./kind-config/cluster-with-nfs.yml \
  --host raftech.localtest.me \
  --dev-password
```