# Micro Kubernetes Distributions for Edge Deployments

## TLDR

- **K3s** (Rancher/SUSE): best default choice for most edge fleets - single ~100MB binary, SQLite by default, huge community, works down to 512MB RAM agents.
- **k0s** (Mirantis): lowest resource footprint, zero OS dependencies, most flexible datastore/CNI/CRI choices - best if you want minimal opinions and full control.
- **MicroK8s** (Canonical): easiest on Ubuntu, single-command add-ons, but requires **snap** and is the heaviest of the three - best for Ubuntu-centric fleets that want built-in HA.
- **KubeEdge**: not a distro, an *extension layer* - use when devices are truly disconnected/intermittent and need offline autonomy, device twins, MQTT/device protocol support. Runs in ~70-100MB on the edge agent but requires a full K8s control plane in the cloud.
- **OpenYurt**: similar edge-autonomy goal to KubeEdge but non-invasive (keeps stock kubelet) - needs beefier edge nodes (~2 vCPU/4GB) than KubeEdge or K3s.

**Default recommendation for typical edge nodes (gateways, industrial PCs, Raspberry Pi-class hardware):** K3s. Fall back to k0s if you need zero host dependencies or a non-systemd/non-standard Linux. Reach for KubeEdge/OpenYurt only if you have genuine offline-first or device-twin/IoT requirements beyond plain container orchestration.

---

## Comparison Table

| Distro | Packaging | Control-plane min | Worker/agent min | Default datastore | Default CNI | Notable caveats |
|---|---|---|---|---|---|---|
| **K3s** | Single binary (<100MB) | 2 vCPU / 2GB RAM | 1 vCPU / 512MB RAM | SQLite (single-node); etcd/MySQL/Postgres for HA | Flannel (VXLAN) | SQLite datastore isn't HA by default; must switch to embedded etcd or external DB for multi-server HA. Built-in `servicelb` (Klipper) and `local-path-provisioner` are basic, not production-grade for RWX storage. |
| **k0s** | Single binary, zero host OS deps beyond kernel | 1 vCPU / 1GB RAM | 1 vCPU / 0.5GB RAM | etcd is default for multi-node; SQLite via kine for single-node | Kube-router (pluggable) | No built-in storage/ingress add-ons out of the box - more assembly required. Younger ecosystem/community vs. K3s. Good for airgapped/custom Linux (no snap, no systemd requirement). |
| **MicroK8s** | Snap package | 2 vCPU / 4GB RAM recommended (can run on 1 vCPU/1GB minimally) | Same (single-node focused; scales via join) | Dqlite (built-in HA clustering) | Calico | Requires **snapd** - a blocker on minimal/embedded Linux or non-Ubuntu distros without snap support. Auto-updates by default (can be disruptive on edge unless pinned/held). Strongest on Ubuntu Core for IoT/edge appliances. |
| **KubeEdge** | CloudCore (cloud) + EdgeCore (edge agent) | Requires a full upstream Kubernetes cluster in the cloud | EdgeCore: as low as ~70-100MB RAM | Uses cloud cluster's etcd | N/A (edge side uses host networking / MQTT for device comms) | Not a standalone cluster - it's a cloud-to-edge extension. Adds real complexity (certs, EdgeHub/CloudHub websocket/QUIC tunnels). Best-in-class offline autonomy: edge nodes keep running and buffering state during cloud disconnects. Built-in device twin/MQTT broker for IoT sensors. |
| **OpenYurt** | Operator + plugins over stock Kubernetes | Cloud control plane + yurt-hub | ~2 vCPU / 4GB recommended | Cloud cluster's etcd | Unmodified K8s CNI | Non-invasive (keeps real kubelet/kube-proxy, easier upgrades/compatibility) but heavier per-node footprint than KubeEdge - better suited to edge *datacenters*/on-prem racks than tiny IoT devices. Less mature ecosystem than KubeEdge. |

---

## Resource Requirements (detail)

**K3s** (official docs.k3s.io):
- Server: 2 CPU / 2GB RAM minimum
- Agent: 1 CPU / 512MB RAM minimum
- Sizing guide: 2 vCPU/4GB server supports ~0-350 agents; scales up to 16+ vCPU/32GB for 1800+ agents
- SSD strongly recommended - etcd/SQLite write I/O will kill SD cards on Raspberry Pi-class hardware

**k0s** (official docs.k0sproject.io):
- Controller: 1 vCPU / 1GB RAM
- Worker: 1 vCPU / 0.5GB RAM
- Controller+worker combined (typical single-node edge): 1 vCPU / 1GB RAM
- Disk footprint: ~0.5GB (controller), ~1.3GB (worker)
- Measured real-world: a controller with 1 worker and 0 pods uses ~510MB RAM

**MicroK8s** (Canonical docs):
- Recommended: 2 CPU / 4GB RAM, 20GB disk
- Can technically run on 1 CPU / 1GB, but tight
- ~1.5GB disk minimum for single node

**KubeEdge**:
- EdgeCore agent: as low as ~70MB RAM footprint on constrained devices
- Requires a separate, fully-provisioned Kubernetes cluster to run CloudCore - so the "edge node" cost is low but the overall system cost is not

**OpenYurt**:
- Edge nodes: ~2 vCPU / 4GB recommended (heavier than K3s/k0s/KubeEdge)
- Best fit: edge server racks/gateways, not micro IoT devices

---

## Feature Support Summary

- **CNCF-certified Kubernetes conformance:** K3s, k0s, and MicroK8s are all CNCF-certified distributions - full kubectl/API compatibility.
- **HA control plane:**
  - K3s: embedded etcd (3+ servers) or external DB (MySQL/Postgres)
  - k0s: embedded etcd or external, plus control-plane load balancing built in
  - MicroK8s: built-in Dqlite-based HA clustering (join 3+ nodes)
  - KubeEdge/OpenYurt: inherit HA from whatever upstream cloud cluster you point them at
- **Offline/disconnected operation:**
  - KubeEdge: purpose-built for this - edge autonomy, local device twin state, reconnect/resync
  - OpenYurt: edge autonomy via yurt-hub caching, but less mature than KubeEdge for long disconnects
  - K3s/k0s/MicroK8s: nodes can keep running existing pods if they lose connection to the server, but there's no special resync/twin logic
- **Device/IoT protocol support (MQTT, Modbus, BLE, etc.):** Only KubeEdge has this natively (via its Mappers/DMI device management framework).
- **Add-on ecosystem:** MicroK8s wins for turnkey add-ons (`microk8s enable dns/ingress/metallb/...`). K3s ships more integrated (Traefik, servicelb, local-path) but swappable. k0s ships minimal by design; you assemble what you need.
- **ARM support:** All five support ARM (arm64/armhf/armv7); K3s has the most field-proven Raspberry Pi track record.

---

## Caveats & Limitations Checklist

- **K3s:** default SQLite = single point of failure unless you configure etcd/external DB HA; Flannel VXLAN needs UDP 8472 open; not ideal if you need a fully vanilla/unmodified Kubernetes networking stack.
- **k0s:** smaller community/support ecosystem than K3s; fewer batteries-included add-ons means more manual setup for ingress/storage; airgapped install requires extra prep.
- **MicroK8s:** hard dependency on snapd (blocks many embedded/minimal Linux images); auto-refresh can silently upgrade unless channel/hold is configured - risky on unattended edge hardware; heavier baseline footprint than K3s/k0s.
- **KubeEdge:** steep operational complexity (certs, tunnels, two separate control loops); requires maintaining a full cloud-side Kubernetes cluster; debugging is harder than a normal cluster; overkill if you don't need offline device autonomy.
- **OpenYurt:** higher per-node resource bar makes it a poor fit for genuinely tiny/constrained devices; smaller adoption/community than KubeEdge; still requires a healthy cloud control plane.

---

## Adoption within industry

**Bottom line:** for oil & gas, **K3s** is the proven choice (ZEDEDA, Portainer, Rancher all target it there). For military/DoD tactical edge, **RKE2** is the compliance-certified answer, often paired with K3s on the smallest nodes.

- **K3s** - the most widely deployed in practice. Rancher/SUSE market it explicitly for "unattended, resource-constrained, remote" sites; vendors like **ZEDEDA** and **Portainer** specifically cite oil rig / SCADA deployments (running alongside legacy Ignition/OT apps) as a target use case. Has official air-gap install support (image tarballs, no internet needed).
- **RKE2** - the one actually *certified* for U.S. defense. It's the only **DISA STIG-validated** Kubernetes distro for DoD/Intelligence Community use, FIPS-enabled (BoringCrypto), SELinux-supported, and used in **Platform One's Big Bang** (DoD's hardened K8s platform). Rancher Government Solutions builds tooling (e.g., "Hauler") specifically for secure air-gapped transfer to tactical sites. This is the default choice when "military" means actual DoD compliance requirements, not just "rugged hardware."
- **K3s + RKE2 together** is the common real pattern: **K3s for lightweight/constrained nodes** (sensors, small gateways, drones), **RKE2 for beefier tactical edge servers** running more complex workloads - both under Rancher's Fleet Manager for GitOps-style fleet management across thousands of disconnected sites.
- **KubeEdge** - used in industrial/telecom edge autonomy scenarios (its origin is Huawei/China telecom + industrial IoT), strong offline device-twin story, but I found no evidence of it in Western defense or major oil & gas deployments - likely provenance/trust concerns for those sectors.
- **OpenYurt / MicroK8s** - no notable evidence of use in defense or oil & gas specifically. MicroK8s' snapd dependency and auto-update behavior work against it in locked-down/offline environments unless carefully pinned.
- **Drones specifically** - no named production case study surfaced (this space is less publicly documented, likely for OPSEC reasons), but architecturally K3s is the natural fit given its footprint (single binary, works offline, ARM-friendly) - it shows up repeatedly in edge/fog-computing UAV research literature as the reference lightweight orchestrator.

---

## Sources

- [K3s Requirements](https://docs.k3s.io/installation/requirements)
- [K3s Resource Profiling](https://docs.k3s.io/reference/resource-profiling)
- [k0s System Requirements](https://docs.k0sproject.io/stable/system-requirements/)
- [k0s GitHub](https://github.com/k0sproject/k0s)
- [MicroK8s Requirements (Canonical)](https://dct.delphix.com/docs/latest/microk8s-requirements-and-prerequisites)
- [MicroK8s vs k3s vs Minikube (Canonical)](https://microk8s.io/compare)
- [Portainer: K0s vs K3s vs Microk8s resource consumption](https://www.portainer.io/blog/k0s-vs-k3s)
- [KubeEdge Docs - CloudCore/EdgeCore Config](https://kubeedge.io/docs/setup/config/)
- [KubeEdge Prerequisites](https://kubeedge.io/docs/category/prerequisites/)
- [Alibaba Cloud: OpenYurt - Extending Native Kubernetes to the Edge](https://www.alibabacloud.com/blog/openyurt-the-practice-of-extending-native-kubernetes-to-the-edge_597903)
- [Titan Wolf: k3s vs OpenYurt vs KubeEdge](https://blog.titanwolf.in/a?ID=023af8dd-d453-46f1-8527-5a8ff28f1d4e)
- [ZEDEDA - Kubernetes at the edge](https://zededa.com/blog/effortlessly-scale-kubernetes-at-the-edge/)
- [Rancher K3s for edge/IoT](https://www.rancher.com/products/k3s)
- [SUSE K3s](https://www.suse.com/products/k3s/)
- [Rancher Government RKE2](https://ranchergovernment.com/products/rke2)
- [DISA validates RKE2 STIG](https://intelligencecommunitynews.com/disa-validates-rancher-government-solutions-stig-for-rke2/)
- [AWS: Deploying RKE2 at the Edge](https://docs.aws.amazon.com/solutions/deploying-rancher-rke2-at-the-edge-on-aws/)
- [K3s Air-Gap Install docs](https://docs.k3s.io/installation/airgap)
