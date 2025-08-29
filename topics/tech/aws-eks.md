# AWS Elastic Kubernetes Service (EKS)

[AWS EKS](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html) is a managed service to run Kubernetes on AWS.

- Integrates with **IAM for authentication**, **ECR for docker container images**, VPC for running the compute **Nodes** (where your **Pods** are _scheduled_) within the cluster.
    
- EKS clusters can be created using AWS Console, AWS CLI or eksctl with JSON template configuration.
    
- Amazon manages the Control Plane for you, within a VPC they manage.
    
    - Single tenant for each cluster.
        
    - At least 2 API server nodes, 3 etcd nodes that span multiple Availability Zones within a Region for high availability. Fronted by a Network Load Balancer.
        
    - Unhealthy Control Plane instances are automatically detected and replaced.
        
    - Automates version upgrades and patches for nodes within the Control Plane.
        
    - Stored data (in etcd and EBS) is encrypted using KMS.
        
    - kubectl communicates with the k8s API server running in the EKS managed VPC as part of the Control Plane.
        
    - The API Server endpoint by default is public to the internet, secured using IAM and k8s Role-based Access Control (RBAC).
        
        - Can limit the public endpoint’s access from specific CIDR blocks (IP address ranges).
            
        - It is possible to make the API server private (not available public on the internet) - a **Private Cluster**.
            
            - This can be done by changing the configuration from Public access to Private access, but this requires a Private VPN Endpoint that will be used for Node to API Server connectivity.
                
            - Clients using kubectl will need to connect from within the VPC or a connected VPN network.
                
            - The security group for the Control Plane must allow ingress traffic on port 443 from the connected network.
                
            - The minimum ports between the Control Plane and Nodes is TCP on 443 and 10250 for inbound and outbound traffic.
                
- **Nodes** run within your VPC, and are either **EC2 instances** (On-demand and Spot instances are supported, can use Savings Plan for them) or part of a **Fargate cluster**.
    
    - **Elastic Network Interfaces** are provisioned in your VPC to provide connectivity from the control plane instances to the Nodes.
        
        - A cluster security group is created to make it a “flat” network where Nodes and the Control Plane can communicate between each other.
            
            - Any instance or network interface assigned to this security group can inter-communicate.
                
            - The security group is attached to the elastic network interfaces.
                
    - Nodes running in your VPC connect to the Control Plane via the API server endpoint, using a certificate created for the cluster.
        
        - Network traffic stays within Amazon’s network
            
        - If the Nodes are deployed into a private subnet without outbound internet egress (called a **Private Cluster**), then:
            
            - Container images must be published to ECR for the Nodes to access them.
                
            - Endpoint Private Access will need to be setup so the Nodes can access the API Server within the Control Plane without routing over the internet.
                
    - EKS can work with a VPC with only public subnets, only private subnets or both public and private subnets. Subnets must have at least 2 Availability Zones.
        
        - When the EKS cluster is created, the subnets are tagged with kubernetes.io/cluster/<cluster-name> so that k8s can discover them. It the tag value is set to shared then multiple EKS clusters can use that subnet.
            
        - Private subnets should be tagged with kubernetes.io/role/internal-elb so that k8s knows to use them for internal load balancers.
            
        - Public subnets should be tagged with kubernetes.io/role/elb so that k8s knows to use them for public internet-facing load balancers.
            
    - The VPC must have DNS hostname and resolution support so that the Nodes can register with the cluster.
        
    - Nodes run in only 1 Region.
        
    - Recommended practice is to deploy Pods to private subnets and Public-facing Load Balancers into Public Subnets to expose only the pods that should be public.
        
    - A **NAT gateway** allows pods to communicate outbound to the internet.
        

- **Managed Node Groups** will be provisioned and have their lifecycle managed by AWS.
    
    - An EKS cluster can have multiple Managed Node Groups.
        
    - Easy to create, update or terminate nodes for the cluster.
        
    - Nodes run the latest EKS optimized Amazon Linux 2 AMIs.
        
    - Node updates and terminations gracefully drain nodes to ensure applications are available.
        
    - Managed nodes are part of an Auto Scaling Group (ASG) that EKS manages.
        
    - The default _allocation strategy_ to provision on-demand Capacity Type uses prioritized, meaning it will attempt to launch instances based on an ordered set of instance types that you specify - e.g. first try c5.large, then c4.large if no c5 are available.
        
    - For Spot instance Capacity Type, the allocation strategy is set to capacity-optimized, so it will automatically attempt to _rebalance_ (and gracefully drain) the Spot nodes if the instance types are at elevated risk of interruption.
        
        - A replacement Spot node is launched.
            
        - After successfully joining the cluster and in Ready state, EKS will cordon the existing node and stop sending requests to it until drained and sending requests to the new node instead.
            
    - The nodes will span the subnets configured as part of the Node Group (which should be a subset of the subnets configured for the EKS cluster). Can be launched in both public and private subnets.
        
    - Managed Node Groups can be added to an existing cluster.
        
    - Managed Node Groups can use a custom AMI, but you are responsible for bugs and security patches.
        
- EKS manages the **upgrade** process for the cluster to migrate to a new version of k8s.
    
    - New API server nodes are launched. If the new nodes are not healthy, the deployment is reverted. This should not impact our existing apps and workloads since the existing API server (running the current version prior to upgrade) will still be in use.
        
        - Some API operations can fail temporarily while the server version is being migrated. Those API calls can be retried.
            
    - 2-3 free IP addresses are required within the subnets associated with cluster.
        
    - Nodes must not run a newer k8s version than the control plane, and should be no more than 1 minor version behind.
        
        - For self-managed nodes, you’ll need to use a new AMI and migrate your pods to a new node group.
            

---

## Links

- [EKS Workshop from AWS](https://www.eksworkshop.com/)

