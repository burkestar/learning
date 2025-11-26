# Kubernetes

## Overview

This page summarizes and synthesizes what I’ve learned about the Kubernetes ecosystem of tools and how they work together, with the goal of making it more approachable and less overwhelming to learn all this stuff.

Recommended O’Reilly course: [Expert Playlist: Certified Kubernetes Application Developer (CKAD) Exam Prep Labs](https://learning.oreilly.com/playlists/8aa87dce-f9a9-4206-83af-c8c730faa430/)

---

## Kubernetes Concepts

Kubernetes (k8s) is an open source system for automating deployment, scaling, and management of containerized applications.

**Kubernetes Objects** are the persistent entities that represent the state of your cluster. They are “records of intent” because they describe what the cluster’s workload should look like and k8s tries to apply changes to achieve this desired state.

- **Namespaces** allow isolating groups of resources within a single cluster.
    
    - Some objects are namespace-scoped (e.g. Deployments, Services, etc.) and others are cluster-wide objects (e.g. StorageClass, Nodes, PersistentVolumes)
        
    - Allows dividing cluster resources between multiple users (via resource quotas)
        
    - There’s a default namespace, kube-public which are accessible to all and kube-system for k8s internals.
        
- **Labels** are metadata attached to objects to organize and select a subset of them when applying changes. See [recommended labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/). The app.kubernetes.io/instance label must be unique as it's the method for differentiating between multiple instances of the same resource, allowing them to co-exist in the same cluster namespace.
    
- **Annotations** are metadata attached to objects that serve a descriptive purpose and can’t be used as selectors like labels.
    
- **Finalizers** specify which actions to attach to resources to control cleanup. It can be attached to a resource to prevent it from being deleted if depended upon by another resource (such as a PersistentVolume attached to a Pod).
    
- **Owners** and **dependents** - some objects can own other objects (as their dependents) such as ReplicaSet is the owner of a set of Pods. The dependent specifies its owner using metadata.ownerReferences field. Namespace-scoped objects can not be owned by resources in another namespace.
    
- **Container** - decouple applications from underlying host infrastructure, including dependencies so they are repeatable whenever its run. Containers are run as part of a pod and colocated to run on the same node. Should be stateless and immutable.
    
    - The imagePullPolicy for a container informs the kubelet when to pull the image from the registry (IfNotPresent, Always, Never).
        
    - Multiple container runtimes (Docker, OCI, etc.) are supported.
        
    - Containers have states (Waiting, Running, Terminated)
        
    - Containers have a restartPolicy - Always (the default), OnFailure, Never. Restarts use exponential backoff (10s, 20s, 40s, … up to 5 mins)
        
    - Container lifecycle hooks allow running custom code during the lifecycle of a container such as PostStart and PreStop events. The handlers can be commands to run in the container or an HTTP request against an endpoint on the container.
        
- **Pod** - smallest deployable unit of a workload in k8s, consisting of a group of Containers with shared storage (volumes) and network resources, and a specification for how to run the containers. The containers within a pod are co-located and co-scheduled, running in a shared context (linux namespace, cgroup and shared filesystem volumes).
    
    - Runs a single instance of an application. Scaling an app horizontally uses multiple pods, one for each instance (replication).
        
    - _**Sidecar**_ containers provide functionality that is tightly coupled and needs shared resources with the other container(s) in the pod.
        
    - **Init containers** run during pod startup. Pods can have one or more init containers, run sequentially. This is useful for running startup scripts not present in the main container’s image.
        
    - **Ephemeral containers** can be used for debugging pods. They can be added to an existing Pod, but not removed. Setup process namespace sharing to allow ephemeral containers to view processes in other containers.
        
    - Pods are an “environment” for running containers and persist until deleted. Containers within the pod may be restarted during the lifetime of a pod.
        
    - Pods are managed by higher level workload resources such as Deployments, StatefulSets, and DaemonSets.
        
    - **PodTemplates** are specifications for creating pods, and are included in workload resources.
        
    - Pods are assigned unique IP addresses, with containers in the pod sharing the network namespace and communicating using localhost and OS-level IPC methods like semaphores or shared memory.
        
    - Containers within the pod have the same system hostname corresponding to the pod name.
        
    - **Probes** are diagnostics performed periodically by the kubelet on a container, either invoking a command within the container runtime or via a network call.
        
        - **livenessProbe** indicates whether the container is running. If fails, kubelet kills the container and subject to restart policy.
            
        - **startupProbe** indicates whether the application within the container is started. If fails, kubelet kills the container and subject to restart policy. Useful for containers with a long startup time (longer than the liveness interval)
            
        - **readinessProbe** indicates whether the container is ready to respond to requests. If fails, the controller removes the pod’s IP address from the endpoints of all Services that match the Pod (since it can’t service these requests)
            
    - Pods have a **lifecycle** with **phases** (Pending, Running, Succeeded, Failed, Unknown). Note that “terminating” status shown by kutectl is not an actual phase.
        
        - The kubelet monitors the pod and its containers, deciding to perform actions on the containers to make the pod healthy.
            
        - Pods are scheduled and assigned to a node only once. The pod runs on the node until it is stopped or terminated.
            
        - Pods do not self-heal but are managed by a higher level controller.
            
        - Dependent resources (such as a volume) with the same lifecycle as a pod will be destroyed when the pod is deleted.
            
    - **PodStatus** is an array of **PodConditions** (PodScheduled, PodHasNetwork, ContainersReady, Initialized, Ready)
        
    - Pods can have a grace period (30 sec default) for termination. The container runtime sends a TERM signal to the main process in each container. Once the grace period expires, the KILL signal is sent to any remaining processes and the pod is deleted from the API Server.
        
    - Pods are garbage collected either manually or by the **Pod garbage collector** which looks for phase of Succeeded or Failed, avoiding resource leaks cleaning up pods when a threshold is exceeded.
        
    - Pods can be disrupted either voluntarily (deleting the workload that setup the pod) or involuntarily (hardware failure, VM termination, kernel panic, network partition makes the node disappear from cluster, pod eviction due to resources). Nodes may be drained by admins for repairing or upgrading them, to scale the cluster down, or to make space on the node for other workloads to use those resources.
        
    - Pods can setup **User Namespaces** for extra security, which maps users within the containers to be different from the users running on the host.
        
    - Pods can use the **Downward API** to lookup information about themselves (e.g. metadata, specs, resources) without using the k8s API.
        
- **Workload Resources**
    
    - **ReplicaSet** - maintains a stable of set replica Pods running at any given time. Specifies a selector to identify pods part of the set, a number of replicas desired, and a pod template for creating new pods. Usually, you should use Deployments instead or ReplicaSets directly.
        
    - **Deployment** - declarative updates for Pods and ReplicaSets, where a Deployment Controller changes the actual state to achieve the specified desired state. A Deployment can manage multiple ReplicaSets for performing a rollout (of new code) or rollback to an earlier revision.
        
    - **StatefulSet** - manages stateful applications by providing guarantees about the ordering and uniqueness of the pods within a deployment. The pods are given a sticky, persistent identifier. Useful when needing stable and unique network identifiers, stable persistent storage, ordered deployment and scaling, or ordered rolling updates. Requires creating a **HeadlessService** to assign unique network identifiers.
        
    - **DaemonSet** - ensures that all (or some) nodes run a copy of a Pod. When new nodes are added to the cluster, the pods are added to them. This is useful to run some daemon process on every node (storage, logging, monitoring).
        
    - **Jobs** - creates one or more pods and continues to retry execution until a specified number successfully terminate. Jobs can be resumed. Jobs can be non-parallel, parallel with a fixed completion count (each pod is assigned an index), or parallel with a work queue (where pods must coordinate on how to divide up the work). Jobs can have a deadline (in seconds) after which the job will be marked failed and pods terminated. Jobs when completed do not delete the pods but are left around for diagnostics - the job needs to be deleted to clean up the pods. A TTL controller can be setup to delete the job after a specified period of time.
        
    - **CronJob** - creates Jobs on a repeating schedule, specified with cron syntax. The CronJob is responsible for creating Jobs that match its schedule and the Job is responsible for the management of the Pods it creates. Name must be 52 characters max, to allow 11 characters automatically appended (timestamp) and fit within the 63 character limit on Job names.
        
    - **ReplicationController** - predates ReplicaSet.
        
- **Services, Load Balancing and Networking**
    
    - **Service** - expresses a way to expose an application running on a set of Pods as a network service.
        
        - Services help frontend clients discover backend endpoints without needing to keep track of the host IPs that these pods are assigned dynamically.
            
        - Services are defined by a selector for pods (that serve as the backend) and exposed port mappings.
            
        - The Service is assigned a virtual IP address (“cluster IP”) which is used by service proxies, and only routable within the cluster network.
            
        - Services without selectors are useful to represent things running external to the cluster (database or legacy apps not yet in k8s)
            
        - EndpointSlices represent a subset of the backing network endpoints for a Service.
            
        - Service discovery works using environment variables (injected by kubelet to the pod’s containers for every running service with a naming convention), or using DNS such as CoreDNS that is cluster-aware to resolve hostnames with name lookup like my-service.my-namespace. DNS SRV queries are also supported to discover ports.
            
        - HeadlessServices have “none” specified for the .spec.clusterIP to avoid allocating an IP.
            
        - **ServiceTypes** supported are **ClusterIP** which exposes the service on a cluster-internal IP only reachable within the cluster, **NodePort** which exposes a static port on the node’s IP, **LoadBalancer** to expose it externally using a cloud provider’s load balancer, or **ExternalName** to return a CNAME.
            
    - **Ingress** - exposes HTTP/HTTPS routes from outside the cluster to Services within the cluster.
        
        - Edge router enforces firewall policy for the cluster, such as a gateway managed by a cloud provider.
            
        - Exposes Services with ServiceType NodePort or LoadBalancer.
            
        - Requires an **IngressController**.
            
        - Specifies **Ingress rules** which are matched against each request. These rules specify the host it applies to, list of paths, and the associated backend where to send the matching request. A default backend is used to route traffic that didn’t match any rules.
            
        - Resource backends are supported for routing to an object storage backend (resources must be in same namespace as Ingress).
            
        - Different patterns can be supported such as ingress backed by a single Service, fan out (matching different requests to different backends, based on path rule matching), or name-based virtual hosting (routing traffic to multiple hostnames at the same IP address, based on host rule matching)
            
        - Provides SSL termination at the ingress.
            
        - Supports load balancing algorithms, backend weighting schemes, etc. but more advanced strategies need to be supported by the load balancer instead of ingress.
            
    - **IngressController**
        
        - Providers an implementation of a gateway or proxy which is used as the class provider for the ingress.
            
        - There are many [supported ingress controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) for API Gateways (e.g. Kong, Envoy) and proxy servers (nginx, haproxy, etc.).
            
    - **EndpointSlice** - references to a set of network endpoints, grouped by unique combinations of protocol, port number and Service name.
        
        - Created automatically based on selectors, including references to all pods that match the Service selector. Owned by the Service to which the endpoint slice refers.
            
    - **Network Policies** control traffic flow at the IP address (CIDR blocks) or port level, by specifying which entities a Pod can communicate with (other pods, other namespaces, IP blocks).
        
        - A selector can be used to determine which Pods the policy should apply to.
            
        - Can restrict pod ingress or egress.
            
            - By default, all ingress and egress are allowed.
                
        - These policies do not conflict and are combined additively (resulting in what the union of these policies allows). It’s possible to create a default deny all policy for all ingress (or egress) and since this is most restrictive, no other policy could allow specific ingress or egress.
            
        - To allow connection from source to destination pod, the egress from source and ingress to destination must both allow it.
            
- **Storage**
    
    - Ephemeral volumes have lifetime same as the pod.
        
    - Persistent volumes lifetime extends beyond lifetime of a pod.
        
    - Data is preserved across container restarts within the pod for all volume types.
        
    - Supports many types of volumes (cloud blob storage like EBS, distributed file stores like GlusterFS, etc.)
        
        - **configMap** - injects ConfigMap configuration into a pod’s containers, where the config keys are mapped to paths on the file system (e.g. /etc/config/log_level).
            
        - **emptyDir** - all containers in the pod can read and write the files in the volume, which is created when the pod is assigned to a node and is initially empty. The data is permanently deleted when the Pod is removed from the node for any reason. Useful for scratch space on disk, checkpointing long computations, temporary holding area.
            
        - **hostPath** - mounts a host file or directory into the pod’s containers. Not very secure, and not recommended unless for very specific needs.
            
        - local - mounted local storage device. Must set a PersistentVolume nodeAffinity when using local volumes so that pods are scheduled on the correct nodes.
            
        - persistentVolumeClaim - mounts a PersistentVolume into a pod.
            
        - secret - used to pass sensitive information to pods. Can mount secrets stored in the k8s API as files for use by pods. Backed by tmpfs (RAM-backed filesystem) so never written to non-volatile storage.
            
    - **PersistentVolume** (PV) is a resource corresponding to some storage provisioned in the cluster. **PersistentVolumeClaims** (PVC) is a request for storage by a user, and represents how a Pod can consume storage resources.
        
        - This separates how storage is provided (PV) from how it is consumed (PVC).
            
        - PVCs are bound to PVs by a controller process
            
        - PVC protection “in use” when a pod exists that is using the PVC, set as a finalizer to prevent data loss.
            
    - Volumes are in status Retained, Recycled, Deleted. Recycle wipes the data in the volume for reuse. Delete destroys the underlying storage (e.g. EBS volume in AWS) from the PV provider.
        
    - Some PVs support expanding the volume to have more storage space, where the existing volume is expanded (rather than a new one created).
        
    - Projected Volumes map several volume sources into the same directory. Supported by secret and configMap volume types.
        
    - StorageClasses can be configured by the cluster admin to apply quality-of-service levels, backup policies or other policies to these resources.
        
    - Dynamic Provisioning for PVs can be configured by the cluster admin by specifying a StorageClass and a provisioner. PersistentVolumeClaims using this storage class will result in dynamically provisioned volumes.
        
- **Configuration**
    
    - Best practices - version controlled config, use YAML, group related settings into a single file, keep it minimal (not repeating defaults), use DNS for service discovery instead of env vars since it requires ordering of service creation before the pod is created.
        
    - **ConfigMap** - an object used to store non-confidential data in key-value pairs.
        
        - Provided to Pods as environment variables, command-line arguments or configuration files in a mounted volume. Code inside the pod could also use k8s API to get ConfigMap settings.
            
        - ConfigMaps are not encrypted.
            
        - Limited to 1MiB
            
        - Can be UTF-8 strings or binary data as base64-encoded strings.
            
        - Keys are alphanumeric wiht -, _ and . allowed.
            
        - ConfigMap must be in same namespace as Pod.
            
        - Updated automatically when mounted in a volume, with period syncing. Not updated when used as env vars, as requires pod restart to pick up changes.
            
        - Can be immutable to to protect against accidental updates and improve performance (no need to watch for updates and sync)
            
    - **Secrets** - an object containing a small amount of sensitive data.
        
        - Encryption at rest must be configured (not enabled by default in etcd used for secrets storage on API server)
            
        - Must configure RBAC rules with least privilege to prevent unauthorized access (by default, any user with authorization to create pods in the namespace can access the secrets)
            
        - Restrict secrets to specific containers
            
        - Consider using external secret store providers
            
        - Can be used as files in a volume mounted in one or more containers in the pod, injected as container environment variables, or by kubelet when pulling images for the pod.
            
        - Same name limitations as ConfigMap.
            
        - Protections
            
            - Secrets are only sent to nodes if a pod on that node requires it
                
            - secrets are mounted using tmpfs so data is not written to durable storage
                
            - kubelet deletes all copies of the secret data once the pods using it are deleted
                
            - secrets must be explicitly specified to be passed to a pod and its containers
                
            - pods do not have access to secrets of another pod (unless containers run with privileged mode! then they can access all secrets stored on the node)
                
    - **kubeconfig** files simplify using kubectl with multiple clusters, specifying how to access the cluster (including authentication and certificates, as needed)
        
        - **Context** in a kubeconfig file is used to group access parameters (cluster, namespace, user) under a convenient name.
            

**Kubernetes** [**Components**](https://kubernetes.io/docs/concepts/overview/components/) are the building blocks of the k8s architecture.

- k8s cluster consists of a set of **nodes** which host **pods**.
    
- **Nodes** are physical or virtual machines managed by the Control Plane and contains services necessary to run Pods (kubelet, container runtime, kube-proxy).
    
    - Nodes can self-register with the control plane, or can be manually added as a node object.
        
    - Nodes must be valid subdomain names and unique to the cluster.
        
    - The conditions field describes the status of the running nodes (Ready if healthy and ready to accept pods, and some unhealthy states to not accept pods - DiskPressure, MemoryPressure, PIDPressure, NetworkUnavailable)
        
    - When problems happen on nodes, the control plane sets **taints** on the node which the scheduler may or may not (tolerations) consider when assigning pods to nodes.
        
    - Nodes have **capacity** (CPU, memory, max number of pods)
        
    - **heartbeats** to determine availability of nodes, which are updates to .status on the node or set as lease objects in the kube-node-lease namespace to reduce the load on the cluster for updating them. The **kubelet** is responsible for updating status and leases for nodes.
        
- **Node controller** managing aspects of a node, monitoring the node’s health (updating node status field, if unreachable will trigger the pod eviction process for pods on that node), keeping the nodes in sync with the cloud provider’s VM resources (making sure the instances are still running), setting up CIDR block assignment.
    
    - node eviction can be rate limited, configured based on cluster size and availability zone (AZ) to handle network partitions or AZ outages more gracefully.
        
- **Control Plane** - manages worker nodes and the pods in the cluster. Makes global decisions about the cluster (scheduling workloads) and respond to cluster events.
    
    - **Kubernetes API** is the frontend for the Control Plane, providing an HTTP API (OpenAPI v2) endpoint. Used by kubectl CLI.
        
        - Supports multiple API versions each at a different API path to make it easier to evolve and present a clear, consistent view of the system resources and behavior.
            
    - **Web UI (dashboard)** - manage and troubleshoot applications running in the cluster
        
    - **kube-scheduler** - assigns nodes to newly created pods based on resource requirements, hardware/software/policy constraints, affinity specifications, data locality, inter-workload interference and deadlines.
        
    - **kube-controller-manager** - runs the controllers to manage nodes, jobs, etc.
        
    - **cloud-controller-manager** - interfaces with cloud providers' APIs
        
    - **kubelet** - an agent running on each node to make sure **containers** are running in a **pod**
        
    - **kube-proxy** - network proxy running on each node to maintain network rules for network sessions between internal and external endpoints.
        
    - **Cluster DNS** - serves DNS records for kubernetes services, configured in containers started by k8s for DNS searches.
        
- Communications within the control plane
    
    - The nodes communicate with the API server with a “hub and spoke” API usage pattern from nodes which terminates at the API server; none of the control plane components expose remote services themselves.
        
    - Communications are secured by default (TLS enabled, root certs should be provisioned on nodes)
        
    - The API server communicates with kubelet using HTTPS or SSH tunneling, and with nodes/pods/services over an unsecured HTTP connection.
        
        - The Konnectivity service replaces the need for SSH tunnels from API server to nodes.
            
- **Controllers** are control loops that watch the state of your cluster, making or requesting changes when needed to achieve the desired state.
    
    - The state of the cluster may never become stable, since changes can be constantly made and fixes are automatically being applied.
        
- **Leases** provide a mechanism to lock shared resources and coordinate activity between nodes. They are used for system-critical things like node heartbeats and component-level leader election.
    

Kubernetes [Security](https://kubernetes.io/docs/concepts/security/)

- 4C’s layer (Cloud, Cluster, Container, Code)
    
    - Cloud - network access to API Server (control plane), nodes, k8s access to cloud provider API, access to etcd, etcd encryption.
        
    - Cluster - securing the components of the cluster and the components in your cluster (apps)
        
        - RBAC, authentication, managing app secrets, ensure pods meet security standards, QoS, network policies, TLS for k8s ingress
            
    - Container - container security scanning, imaging signing, disallow privileged users, use container runtime with stronger isolation
        
    - Code - access over TLS only, limiting port ranges of communication, 3rd party dependency scanning, static code analysis, dynamic probing attacks
        
- Pod security standards
    
    - Profiles (Privileged, Baseline, Restricted)
        
- [Multitenancy](https://kubernetes.io/docs/concepts/security/multi-tenancy/)
    

[Extending Kubernetes](https://kubernetes.io/docs/concepts/extend-kubernetes/)

- Patterns
    
    - Controller pattern - read objects .spec and update object’s .status. Client of the k8s API.
        
    - Webhooks can make network request to external endpoints.
        
    - Binary plugins can be executed by kubernetes within the kubelet (e.g. plugins for devices, storage, network, scheduler)
        
- Extension points
    
    - kubectl CLI plugins
        
    - API Access extensions
        
    - API extensions for built-in resource kinds
        
    - Scheduler extensions
        
    - Controllers
        
    - Network plugins
        
    - Device plugins
        
- [Operators](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) - software extensions that make use of custom resources to manage apps and their components.
    
    - Custom Resource Definitions (CRD) defines new resource objects available within the k8s API.
        
    - Associate Custom Resource with a Controller
        
    - Controller runs outside the control plane.
        

---

## kubectl for interacting with cluster resources

See [**cheatsheet**](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

Create namespace

`kubectl create namespace mynamespace`

Set which namespace to use by default

`kubectl config set-context --current namespace=mynamespace`

Run nginx pod

`kubectl run nginx -n mynamespace --image=nginx --restart=Never --port=80`

Run interactive shell in a pod

`kubectl run busybox -n mynamespace --image=busybox --rm -it -- sh`

Get pod logs

`kubectl logs nginx -n mynamespace`

---

## [Tilt](https://docs.tilt.dev/) for local k8s development

Tilt automates a local k8s development environment from watching files, building container images and bringing up k8s resources.

Tilt supports Helm but does not require it, and it works with k8s API directly. Helm is only used to render resource manifests which are consumed by Tilt which now manages the resources in k8s.

**Supported**

- OS: Mac, Linux, Windows
    
- k8s [clusters](https://docs.tilt.dev/choosing_clusters.html): Kind, Docker Desktop, Microk8s, Minikube, k3d, Rancher Desktop, AWS EKS, Azure AKS, Google GKE.
    

**Functionality**:

- **Tiltfile** - a program written in Starlark (dialect of python with better security and isolation), to define the development environment.
    
    - Executed on startup by Tilt.
        
    - Can use control loops, functions, data structures.
        
    - Built-in functions are provided like docker_build to build images, k8s_yaml for constructing k8s resources from a YAML manifest, etc.
        
    - The resulting configuration is used to assemble resources to build and deploy in k8s.
        
    - Tilt watches relevant files to trigger a rebuild and redeploy of associated resources.
        
    - Supports Custom commands using local built-in function
        
- **Tilt web UI** to see resource status, logs, and manage resources.
    
- **Smart rebuilds** so that the right things get rebuilt at the right times.
    
- Performs **in-place live updates** of containers in cluster for faster turnaround.
    
- Supports [Helm charts](https://docs.tilt.dev/helm.html) using helm_resource extension for Helm repos and k8s_yaml(helm('./chart'))
    
- Supports [local and remote](https://docs.tilt.dev/local_vs_remote.html) clusters, as well as “hybrid” to blend local and remote resources.
    
- Use cases
    
    - Sync source code changes to running container
        
    - Dependency changes synced to running container
        
    - Build spec changes (e.g. Dockerfile) - rebuild image and redeploy
        
    - Deploy spec changes (e.g. app.yaml) - reconcile deployment state (e.g. kubectl apply)
        

**Tiltfile**:

**Control loop:**

---

## Helm for deployment

**Concepts**:

- **Chart** is a Helm package, containing all the resource definitions necessary to run an app inside a k8s cluster.
    
- **Repository** is a place for charts to be collected and shared.
    
- **Release** is an instance of a chart running in a k8s cluster, with multiple releases of the same chart running in the same cluster.
    
- Helm installs _charts_ into Kubernetes, creating a new _release_ for each installation. And to find new charts, you can search Helm chart _repositories_.
    

**Architecture**

- **Helm Client** is a command line interface for developing charts, managing repositories and releases and interfacing with the Helm Library
    
- **Helm Library** is a reusable library that interfaces with the k8s API server and combines chart and config to build a release and interacts with k8s to install, upgrade and uninstall charts
    
- Developed in golang.
    
- Uses REST+JSON with k8s API server.
    
- Stores Secrets inside k8s.
    
- Does not need it own database.
    
- Local configuration provided by YAML files.
    

**CLI**:

- helm search to find charts in repositories. The Artifact Hub aggregates charts from dozens of repositories.
    
- helm install installs a package, taking the release name and the name of the chart.
    
    - Resources are installed in a specific order.
        
    - Helm does not wait until all resources are running before exiting, since they may take awhile.
        
    - Tip: use helm install someRelease someChart/ --debug --dry-run to check the rendered template.
        
- helm status to look up status of a release.
    
- helm upgrade takes an existing release and upgrades it, only updating things that have changed.
    
- helm rollback recovers from failure back to an earlier release version (revision numbers start with 1)
    
- helm uninstall to uninstall a release and delete its associated resources.
    
- helm list --all to see all releases on the cluster.
    
- helm repo list to see repos configured, helm repo add to add another repo, and helm repo remove to delete a repo.
    
- helm create to create charts.
    
- helm lint to validate charts.
    
- helm package to package up a chart (as tar gzip) for distribution to a repository.
    

**Charts**

- Organized into a directory of files by convention. Directory name is the chart name.
    
    - chart/
        
        - Chart.yaml - YAML file describing the chart
            
        - values.yaml - default configuration
            
        - charts/ - directory containing dependent charts
            
        - crds/ - custom resource definitions
            
        - templates/ - directory of templates that are combined with values to generate valid k8s manifest files
            
- Chart names must be lower case letters and numbers, separated with -.
    
- YAML files use two spaces (never tabs) as a best practice
    
- Charts are semantically versioned
    
    - apiVersion field specifies which Helm version (range) is required by the chart
        
    - appVersion field specifies the version of the app packaged in the chart.
        
    - kubeVersion field specifies which k8s cluster version (range) is required by the chart
        
- Chart types are application and library.
    
    - Application are the most common and render k8s resource objects.
        
    - [Library charts](https://helm.sh/docs/topics/library_charts/) provide utilities and functions for chart building but are not installable and don’t contain resource objects. Useful for code reuse across charts.
        
- Chart dependencies
    
    - Organized in the charts/ subdirectory. Either packaged as a chart archive or unpacked into a subdirectory (for more control).
        
    - When referenced (dynamically linked) using the dependencies field in Chart.yaml
        
        - The dependency specifies the name, version and repository of the chart, where the repo must be added locally to Helm using helm repo add.
            
        - helm dependency update will use the dependencies field to download the specified charts into the charts/ directory as archives (.tgz).
            
        - helm pull will download the dependent chart into charts/ directory and unpack it.
            
    - tags and condition field support enabling/disabling subcharts from the parent chart. Conditions override tags. Tags are evaluated as “if any of the chart’s tags are true, then enable the chart”.
        
    - Subchart values can be propagated to the parent chart using exports in the subchart’s values.yaml and dependencies.import-values within the parent Chart.yaml.
        
    - When installing helm install or upgrading helm upgrade a release, Helm aggregates all the charts and subchart dependencies into a single set, sorts them by type and name, then creates/updates them in that order. A Helm release results in the creation of all objects for the chart and its dependencies.
        
    - Best practice for complex apps: use top-level umbrella chart that exposes global configuration and then use a charts/ subdirectory to embed each of the components
        
- Templates
    
    - Uses [Go templates](https://pkg.go.dev/text/template) for templating resource files.
        
    - Add-on template functions
        
        - Includes [Sprig library functions](https://github.com/Masterminds/sprig) for use in templates
            
        - include to include another template
            
        - required specifies a map key that is required to render the template
            
    - When Helm renders the chart, every file in templates/ directory is passed through the template engine.
        
    - Files prefixed with _ by convention are not expected to output a k8s manifest file. Helper templates and partials are commonly named like _*.tpl and _*.yaml.
        
    - Best practices for templates
        
        - Use dashed notation for file names (e.g. my-example-configmap.yaml)
            
        - Each resource definition in its own template file
            
        - Template file names should reflect the resource kind in the name (e.g. foo-pod.yaml, bar-svc.yaml)
            
        - Defined templates (created inside a {{ define}} directive) are globally accessible and thus should be namespaced (e.g. {{- define "nginx.fullname" }})
            
        - Minimize whitespace in generated templates ({{- print "foo" -}})
            
        - Use Helm comment style for the template itself (/* some template comment */) and YAML-style comments to include them in the rendered YAML (# inline comment for yaml)
            
- Values
    
    - Values are supplied to the template by values.yaml file (think of these as “defaults”) or a YAML file provided on the command line to helm install --values=myvals.yaml
        
        - Additional values files passed as arguments are merged with values.yaml and will override the defaults from values for any name collisions.
            
    - Values file is YAML.
        
    - Values can be referenced in templates using .Values.someValue.
        
    - Best practices for values
        
        - Name conventions
            
            - Should begin with a lowercase letter, and camelCased.
                
            - Case-sensitive.
                
            - Don’t use initial caps (to distinguish from built-in values which are capitalized)
                
            - Do not use hyphens
                
        - Favor flat or nested values for simplicity.
            
            - Nested values need to check existence at every layer of nesting for safety.
                
            - When there are a large number of related values and at least one of them is non-optional, nested values may be used for readability.
                
        - Make types clear
            
            - Be explicit about strings and implicit about everything else.
                
                - Quote all strings (e.g. name: {{ .Values.MyName | quote }})
                    
                - Use {{ int $value }} in templates
                    
            - Document every value
                
    - Predefined values are available to every template and cannot be overridden.
        
        - .Release.Name - name of Helm release, not the chart
            
        - .Release.Namespace - namespace the chart was released to
            
        - … some others like .Release.Service, .Release.IsUpgrade, and .Release.IsInstall
            
        - .Chart provides contents of Charts.yaml such as .Chart.Version
            
        - .Files is a map-like object to reference files within the chart.
            
        - .Capabilities is a map-like object about the versions of Kubernetes and supprted k8s API versions.
            
    - Any unknown fields in Chart.yaml will be dropped and can not be used to pass arbitrary data into the template (use the values file instead)
        
    - Charts at a higher level have access to all the variables in subcharts, but lower level charts cannot access things in the parent chart.
        
        - Values are namespaced as .Values.subChart.someField in the parent chart but pruned in the subchart (.Values.someField) without the namespace prefix
            
        - (support for global values is coming in Helm 2.0 that allows subcharts to access values from the parent…)
            
    - Schema files
        
        - Optional, values.schema.json is a JSON Schema to represent what is allowed in the values.yaml file, and is used to validate it with helm install, helm upgrade, helm lint, helm template commands.
            
        - The final .Values object is checked against all subchart schemas, so parent charts can’t circumvent restrictions on a subchart.
            
- Custom Resource Definitions (CRD)
    
    - CRD YAML files go in /crds directory inside chart.
        
        - Cannot be templated
            
        - Multiple CRD definitions can be in the same YAML file.
            
    - Helm will load all of the files in CRD directory to k8s, and are installed before the rest of the chart.
        
        - Helm install will upload CRDs, pause until CRDs are available to API server, start template engine, render the rest of the chart, upload the resources to k8s.
            
    - CRD information is available in Helm templates using .Capabilities object and can create instances of these objects
        
    - CRDs are installed globally on the cluster
        
        - Helm never reinstalls them, never installs on upgrade or rollback, and are never deleted.
            
        - These are safeguards to prevent issues with managing CRDs, and require operators to upgrade or delete CRDs manually.
            

**Repositories**

- Any HTTP server that can serve HALM and tar files and can answer GET requests can be used as a repo server.
    
    - Google Cloud Storage and S3 in website mode can be used as repository servers
        
- Exposes an index.yaml that lists all the packages and their metadata
    

[**Chart Hooks**](https://helm.sh/docs/topics/charts_hooks/)

- Allows performing operations at specific points in the release lifecycle.
    
- Hooks
    
    - pre-install executes after templates are rendered but before any resources are created in k8s
        
    - post-install executes after all resources are loaded into k8s
        
    - pre-delete - executes on a deletion request before any resources are deleted from Kubernetes
        
    - post-delete - executes on a deletion request after all of the release's resources have been deleted
        
    - pre-upgrade - executes on an upgrade request after templates are rendered, but before any resources are updated
        
    - post-upgrade - executes on an upgrade request after all resources have been upgraded
        
    - pre-rollback - executes on a rollback request after templates are rendered, but before any resources are rolled back
        
    - post-rollback - executes on a rollback request after all resources have been modified
        
    - test - executes when the Helm test subcommand is invoked
        
- Hooks can be blocking operations so Helm client will pause until it successfully runs to completion, and if it fails then the release fails. Other non-blocking hooks only wait until k8s marks the resource as loaded.
    
- Hooks can define a weight to determine a partial ordering for how they are applied (in ascending order). Use 0 if the weight is not important.
    
- Any resources created by hooks are not managed by Helm (and not garbage collected)
    
- Hooks are written as k8s manifest files with special annotations in metadata section and can be templated in Helm.
    

[**Chart Tests**](https://helm.sh/docs/topics/chart_tests/)

- A test is defined as a job under the templates/ directory that specifies a container with a given command to run, and uses annotation helm.sh/hook: test.
    
    - Exit with 0 if test was successful.
        
    - Suggested to organize them in templates/tests/ subdirectory
        
- Run tests with helm test <releaseName>
    

[**Provenance and Integrity**](https://helm.sh/docs/topics/provenance/)

- Charts can have a corresponding provenance file (e.g. myapp-1.2.3.tgz.prov) that sit alongside the chart archive, are generated at packaging time (using --sign arg) and can be verified like helm install --verify.
    
    - Requires a valid PGP keypair in binary format
        
    - helm package --sign --key 'John Smith' --keyring path/to/keyring.secret mychart
        
    - Contains: the Chart.yaml , a SHA256 hash of the chart package (.tgz file), and a PGP signature of the entire body
        

[**Helm Plugins**](https://helm.sh/docs/topics/plugins/)

- Extend the Helm CLI with new commands
    
- Plugins stored in $HELM_PLUGINS directory (check helm env for the path)
    
- helm plugin install <path|url> which will copy the plugin into the $HELM_PLUGINS directory
    
- Plugins use a convention-based folder structure:  
    
    - The folder last is the name of the plugin itself and corresponds to helm last command.
        
    - plugin.yaml defines the commands (binaries or scripts to run with specific args) which can be configured per platform (OS like linux and arch like amd64)
        
    - environment variables from the outer environment are passed to the plugin plus some special HELM_ variables.
        
    - Can define shell completions using completion.yaml file.
        

---

## Miscellaneous

### Tools

- [ctlptl](https://github.com/tilt-dev/ctlptl) (cattle patrol) CLI for declaratively setting up k8s clusters using YAML. Supports Docker Desktop, KIND, Minikube, k3d but not yet microk8s.
    
    

---

## CKAD Exam Prep

- [CKAD exam](https://www.cncf.io/certification/ckad/)
    
    - costs $399 (1 free retake)
        
    - 2 hour time limit
        
    - 15-20 exercises (~6 mins per question). No multiple choice.
        
    - 66% is passing score. Scored automatically within 24 hours.
        
    - All exercises are tasks to be completed within a Linux bash terminal in your browser which is connected to the exam environment which has kubectl and access to a Kubernetes cluster.
        
    - The exam environment is done online and will restrict which browser tabs you can have open, monitor all your connected displays and have your webcam on. They are serious about having your desk space clear of everything except water.
        
    - Most of the exercises are based on kubectl commands (imperative) so you should be very comfortable using kubectl.
        
    - You are allowed to reference some online k8s and Helm documentation. See [resources allowed](https://docs.linuxfoundation.org/tc-docs/certification/certification-resources-allowed#certified-kubernetes-administrator-cka-and-certified-kubernetes-application-developer-ckad).
        
    - See [FAQ](https://docs.linuxfoundation.org/tc-docs/certification/faq-cka-ckad-cks) for more
        
- Additional resources
    
    - [https://learning.oreilly.com/playlists/8aa87dce-f9a9-4206-83af-c8c730faa430/](https://learning.oreilly.com/playlists/8aa87dce-f9a9-4206-83af-c8c730faa430/)
        
    - [Kubernetes Design Principles](https://www.youtube.com/watch?v=ZuIQurh_kDk) - 50 minute video, recommended by @Artsiom Kaval as the best place to start.
        
    - [CKAD Exercises for practicing](https://github.com/dgkanatsios/CKAD-exercises)
        
    - [Kubernetes environment in Katacoda](https://oreil.ly/Uucxp) (available in O’Reilly) for a live k8s environment for practicing
        
    - Blog article with [tips on passing the CKAD exam](https://www.telesens.co/2021/02/08/passing-the-ckad-exam/)
        
    - [Certified Kubernetes Application Developer (CKAD) Prep Course](https://oreil.ly/MFAjT) is a video-based Learning Path on the O’Reilly Learning Platform
        
    - [Certified Kubernetes Application Developer (CKAD) Cert Prep: Exam Tips](https://oreil.ly/MPofV) is a video-based course on LinkedIn Learning that focuses exclusively on exam preparation.

    - [Killercoda](https://killercoda.com/) interactive environments in your browser, geared towards learning and certifications
