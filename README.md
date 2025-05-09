![Logo](https://github.com/raftechio/.github/blob/6f694eb425c2ec275e41a7677941b8f8d957b2b5/github-repo-logo.jpeg)

# cnoe-stacks-custom

Custom set of configuration/stacks for CNOE idpbuilder

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
