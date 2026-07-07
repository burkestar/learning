# Multi-cluster Kubernetes

Compute orchestration for deploying workloads across multiple clusters.

## Benefits

- Infrastructure elasticity to scale beyond the compute capacity and limits within a single cluster and data center
- Optimize workload placement based on compute costs (cheaper hardware), resource availability (GPU hardware), performance (network latency, data locality)
- High availability and disaster recovery for critical workloads to survive data center outages
- Geo distribution for locality to data or client access
- Physical isolation for security and compliance
- Multi-cloud independence to avoid vendor lock-in

See [why multicluster](https://multicluster.sigs.k8s.io/#problem-statement-why-multicluster) from k8s SIG-Multicluster.

---

## Fleet Management

Automating the lifecycle for multiple clusters.  Provisioning, upgrading and configuring to minimize the effort to manage more clusters. Ensuring they stay up-to-date and have consistent configuration applied to avoid drift.

### Tools

- **[Cluster API (CAPI)](https://cluster-api.sigs.k8s.io/)** - declarative cluster provisioning/upgrades, treats clusters as managed K8s resources - from [sig-cluster-lifecycle](https://github.com/kubernetes/community/tree/main/sig-cluster-lifecycle#readme)
- **[Crossplane](https://www.crossplane.io/)** - infra-as-code control plane, provisions clusters + cloud resources declaratively; open source (Apache 2)
- **[Sveltos](https://projectsveltos.io/latest/)** - lightweight policy/config management across cluster fleets; open source (Apache 2)
- **[Rancher](https://rancher.com/docs/)** - centralized multi-cluster lifecycle management; open source (Apache 2), vendor backed for Enterprise support
- Commercial
	- **Platform9** - hosts control plane, manages clusters on infra you own
	- - **[Red Hat Advanced Cluster Management (RHACM)](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/2.0/html/about/welcome-to-red-hat-advanced-cluster-management-for-kubernetes)** - governance + lifecycle, enterprise-grade

---

## Workload Federation

Coordinating and managing resources across multiple independent clusters that are unified to provide a consistent experience although each cluster has its own control plane and autonomy for managing node health, capacity, and workload stability.

Control Hub cluster pushes policy, configuration, and workload placement decisions to member clusters within the federation.

Each member cluster operates independently so that if the hub has an outage, member clusters keep running on their last applied configuration for resiliency.

What gets federated:

- **workload placement** - to run workloads on multiple clusters
- **configuration and policies** - RBAC, network policy, resource quotas applied consistently across the fleet
- **service discovery** - so that a service in one cluster can discover and communicate with a service in another cluster
- **identity and cluster metadata** - knowing which clusters exist, what resources and capacity they provide

### Tools

- **[Karmada](https://karmada.io/)** - k8s native to deploy workloads across k8s clusters and clouds, providing centralized multi-cloud management, high availability, failure recovery, and traffic scheduling; open source (Apache 2), CNCF incubating
- **[Open Cluster Management (OCM)](https://open-cluster-management.io/)** — manages cluster inventory, work distribution, workload placement across multiple k8s clusters; open source (Apache 2), CNCF sandbox
- **[Submariner](https://submariner.io/)** - a tool built to connect overlay networks of different Kubernetes clusters, designed to be network plugin (CNI) agnostic and supports both encrypted and non-encrypted tunnels between the connected clusters; open source (Apache 2)
- **[KubeFleet](https://kubefleet.dev/)** — schedule workloads intelligently, roll out changes progressively, and perform administrative tasks easily, across a group of Kubernetes clusters in any location; open source (Apache 2), CNCF sandbox
- **[Nova](https://github.com/elotl/try-nova)** — policy-driven multi-cluster control plane that allows you to treat a fleet of Kubernetes clusters as a single logical compute fabric, automatically placing workloads on workload clusters based on user-defined policies; open source (Apache 2) but looks like it will go opencore with a vendor
- **ArgoCD** - GitOps tooling for deploying workloads to clusters
- **Flux** - GitOps tooling for deploying workloads to clusters

### Notes

- Gang scheduling means all-or-nothing scheduling — a job's pods only start if all required pods can be placed simultaneously; otherwise none start (avoids partial-start deadlocks in distributed training)
- Cross-cluster gang scheduling is immature — most tools do this well within one cluster; multi-cluster gang scheduling is still an edge case, not a solved standard
- Cross-cluster federation tools (Karmada, OCM) handle "which cluster gets this job" — gang scheduling then happens within that cluster via Volcano/Kueue

---

## Standards and Specs

- **[Kubernetes SIG-Multicluster](https://multicluster.sigs.k8s.io/)** for governance of standards
	- **[About API](https://multicluster.sigs.k8s.io/concepts/about-api/)** allows to uniquely identify clusters within a set of clusters (clusterset)
	- **[Multicluster Services API](https://multicluster.sigs.k8s.io/concepts/multicluster-services-api/)** - allows to expose services across clusters which are part of a given clusterset.
	- **[Work API](https://multicluster.sigs.k8s.io/concepts/work-api/)** - allows to define the workloads to be deployed across clusters which are part of a given clusterset.
	- **[ClusterProfile API](https://multicluster.sigs.k8s.io/concepts/cluster-profile-api/)** - simplifies the discovery and management of clusters, offering a comprehensive status overview for both applications and human operators.
	- **[Kubernetes Enhancement Proposals (KEP)](https://github.com/kubernetes/enhancements/tree/master/keps/sig-multicluster)**
- **[Gateway API](https://gateway-api.sigs.k8s.io/)** for traffic management (north/south and east/west with emerging **[GAMMA](https://gateway-api.sigs.k8s.io/docs/mesh/gamma/)** spec for service mesh)
- **[CNCF Landscape for Orchestration and Management](https://landscape.cncf.io/guide#orchestration-management)** for open source projects under Cloud Native Computing Foundation's (CNCF) governance

---

## Trends

- **"Fleet as unit of design"** — teams stop managing clusters individually; they define one GitOps contract/policy set applied across all clusters via CAPI + Karmada/OCM Fleet and cluster API integration will make _"many clusters, one contract"_ a standard pattern
- **GitOps consolidation** — Argo CD/Flux remain dominant, but focus shifts from "which tool" to running them at fleet scale: OCI-native chart distribution, progressive delivery (canary/blue-green) built in, multi-cluster consistency by default
- **OCI as default artifact format** — Helm charts/artifacts move to OCI registries as primary distribution path (old chart repos coexist but aren't primary)
- **AI/GPU workloads as first-class multi-cluster citizens** — gang-scheduling (Volcano), quota management (Kueue) now factor into cross-cluster placement decisions, not just single-cluster scheduling
- **Cost optimization becomes a platform responsibility** — baked into platform contracts via policy engines (Gatekeeper/Kyverno), not an afterthought; enforced across the fleet uniformly
- **Zero-trust/security policy fleet-wide** — image signing (Sigstore/Cosign), eBPF runtime security (Falco/Tetragon) expected consistently across all clusters, not per-cluster config
- **Gateway API replacing Ingress** — driven by Ingress-NGINX deprecation; migration tooling exists to translate old Ingress → Gateway/HTTPRoute in parallel during transition
