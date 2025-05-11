![Logo](https://github.com/raftechio/.github/blob/6f694eb425c2ec275e41a7677941b8f8d957b2b5/github-repo-logo.jpeg)

# cnoe-stacks-custom

Custom set of configuration/stacks for CNOE idpbuilder

## Using the custom stacks

### Referencing the custom stacks

As some of the references require OCI repositories there is file prepared by us for that that enables OCI repositories for custom stacks we included. 

> File name is `override.argocd-cm.yaml` and it is located in the root of the repository.You will need to reference it when creating the stack.

```bash
idpbuilder create \
  -p https://github.com/raftechio/cnoe-stacks-custom/custom/kro \
  -c argocd:./override.argocd-cm.yaml
```

### Referencing the custom cluster configuration
As the idpbuilder supports referencing the kind config from a remote source, we can use the following command to create the stack with a more complex configuration.

```bash
idpbuilder create --kind-config https://github.com/raftechio/cnoe-stacks-custom/blob/main/custom-cluster.yaml 
```

## Custom clusters

In order to more advanced ( read: realistic ) cluster configurations, we are using custom cluster configuration. Below you may find the outlines of our configurations

<details>
<summary><strong>Cluster topology - <code>custom-cluster.yaml</code></strong></summary>

#### Control Plane
- **1 control-plane node**
  - Exposes ports: 8443 (HTTPS), 32222 (custom)
  - Labeled for ingress readiness
  - Controller, scheduler, and etcd metrics are accessible on all interfaces

#### Worker Nodes

**Ingress Gateway Nodes**
- **1 external ingress gateway**
  - Exposes: 88 (HTTP), 444 (HTTPS)
  - Zone: eu-west-1a
  - Labels: gateway, nginx controller, external type
  - Taints: restricts scheduling to gateway workloads
- **1 internal ingress gateway**
  - Exposes: 89 (HTTP), 445 (HTTPS)
  - Zone: eu-west-1b
  - Labels: gateway, nginx controller, internal type
  - Taints: restricts scheduling to gateway workloads

**Core and Application Nodes**
- **1 core worker**
  - Zone: eu-west-1a
  - Labels: core workload
- **3 ondemand app workers**
  - Zones: eu-west-1a, eu-west-1b, eu-west-1c
  - Labels: ondemand, apps
  - Taints: restricts scheduling to app workloads

**Spot and Preview Nodes**
- **1 spot app worker**
  - Zone: eu-west-1c
  - Labels: spot, apps
  - Taints: restricts scheduling to app workloads
- **3 spot preview workers**
  - Zones: eu-west-1a, eu-west-1b, eu-west-1c
  - Labels: spot, preview
  - Taints: restricts scheduling to preview workloads

### Node Affinity and Scheduling
- Nodes are labeled and tainted for fine-grained scheduling:
  - Node types: ondemand, spot, core, gateway, preview
  - Gateway nodes are further labeled/tainted for controller type (nginx) and gateway type (internal/external)
  - Zones are simulated via topology labels (eu-west-1a/b/c)

### Port Mappings
- Ingress nodes map cluster ports 30080/30443 to host ports for local access.

This setup is ideal for testing multi-zone, multi-role scheduling, ingress controller scenarios, and simulating production-like taints/labels in a local Kind cluster.

</details>




