# AWS Virtual Private Cloud (VPC)

### Regions and Availability Zones

- AWS provides global infrastructure, with data centers in different **Regions** of the world.
    
- Each region may have one or more **Availability Zones** (AZs) - which are isolated data centers within the region. By distributing your resources across multiple AZs, this helps increase the availability of your system by increasing the likelihood that even if one AZ were to go offline, resources in the other zones are unaffected and still available.  
    

### **Virtual private cloud (VPC)**

A virtual network dedicated to your AWS **account**.

- A VPC is _launched within a **single Region**_, but can _**span all that region’s Availability Zones**_.  
    
    ![](https://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-diagram.png "Attachment")
    
- An AWS account can have **multiple VPCs** (default quota is 5, but can be increased).
    
- An organization can have **multiple accounts**, and AWS provides ways to share resources and route network traffic between accounts.
    
- The virtual network is a _**software-defined network**_, meaning it emulates the physical network hardware but allows you to program and configure it as software.
    

### Subnets

A **range of IP addresses** in your VPC.

- Resources like EC2 instances (i.e. a virtual server) need an IP address allocated so they are routable, so resources are launched within a specified subnet.
    
- A **private subnet** is for resources that won’t be connected to the internet whereas a **public subnet** is for resources that must be connected to the internet.
    
- To protect resources within each subnet, multiple layers of security are provided in the form as **security groups** (secgroup) and **network access control lists** (NACLs).
    
- Usually represented as a **CIDR block** with an IP address prefix for the network and a slash number (for the bit mask).
    
- For example, a 10.0.0.0/8 would set the network to 10.x.x.x and allow up to 16,777,214 hosts (256 bits * 256 * 256 ) to be assigned IP addresses within the subnet. 10.12.0.0/16 would set 10.12.x.x as the network and allow up to 65,534 hosts. A /24 would allow 254. You get the idea. 0.0.0.0/0 represents the full universal of all possible hosts. An address like 10.20.30.40/32 would be that single IP.
    
- The CIDR block for a subnet _**must not overlap any other block**_ within the VPC.
    
- The CIDR block size must be between a /28 and /16 netmask.  
    

- **Private vs Public IP addresses** -
    
    - Private IP addresses are not reachable over the internet, and can be used for communicating between instances in your VPC. When an instance is launched, a private IP is assigned to the default network interface (eth0) of the instance from the available IP addresses within the subnet.
        
    - Public IP addresses are reachable from the internet. Addresses are assigned from Amazon’s pool of public IP addresses not associated with your account.
        
        - A public IP will be associated with a newly launched instance if the subnet has a property set to automatically assign a public IP.
            
        - **Network Address Translation (NAT)** is how a public IP address is mapped to the private IP address of the instance and traffic is routed.
            

### Route Table

A set of rules, called routes, that are used to determine where network traffic is directed.

- A subnet is associated with a particular route table for how its traffic will be routed.
    
- Each route in a route table specifies the range of IP addresses where you want the traffic to go (the _**destination**_) and the gateway, network interface, or connection through which to send the traffic (the _**target**_).
    
- For the example below:
    
    - hosts within the subnet using this route table that are trying to reach addresses in 172.31.0.0/15 will route through the pcx-112233445566778899 **peering connection** (pcx). The **Peering Connection** is a _network connection between two VPCs_ that enables traffic to be routed between them privately, as if they were in the same network.
        
    - Traffic intended for the internet (0.0.0.0/0) will route through the Internet Gateway (igw-1234568901234567).  
        
- Here is a more complete picture of a VPC with multiple subnets across several availability zones, and various gateways to connect with the world (internet gateway) or a corporate network (virtual private gateway). Subnet 1 is a _**public subnet**_ since it has an internet gateway, subnet 2 is a _**private subnet**_, and subnet 3 is a _**VPN-only subnet**._  
    
    ![](https://docs.aws.amazon.com/vpc/latest/userguide/images/subnets-diagram.png "Attachment")
    

### Internet Gateway

A gateway that you attach to your VPC to enable communication between resources in your VPC and the internet.

### Security Groups and NACLs

A **Security Group** is a virtual firewall that controls inbound and outbound _**traffic for your instances**_.

- Up to 5 security groups can be assigned to an instance.
    
- There’s a different set of _**rules**_ for inbound traffic from outbound traffic.
    
- **Rules**
    
    - specify what is allowed, not what is disallowed
        
    - _filter traffic by protocol and port numbers_
        
    - Can filter based on the _source of the traffic_ - whether specific IP address blocks, the internet, or from another subnet in your VPC.
        
    - **stateful** - if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules. Responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules.
        
    - Security groups are _**associated with network interfaces**_. They are attached to the default network interface for the instance, unless specified otherwise. An instance with multiple network interfaces can have different security groups attached.
        
- Example that shows sample rules for inbound traffic by protocol, port range and the source for where the traffic is coming from.  
    

A [**Network Access Control Lists**](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) **(NACL)** - A virtual firewall to control inbound and outbound _**traffic for your subnets**_.

- Considered an optional secondary line of network defense (to complement security group rules).
    
- Since security groups apply to an instance, the destination of the traffic is assumed to be that host. However, NACLs define rules between hosts within the subnet so its possible to _**filter by source and destination**_.
    
- Whereas security groups only specify ALLOW rules, NACLs _can define ALLOW and **DENY** rules_ for traffic.
    
- Rules are _evaluated in order_, starting with the lowest numbered rule. The _**first matching rule is applied**_, and **short circuits** (so higher numbered rules are not triggered even if they would match).
    

## Links

- [https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)