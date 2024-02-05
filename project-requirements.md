# Creating a single internet exit point from multiple VPCs Using AWS Transit Gateway

## Centralizing Outbound Internet Traffic with AWS Transit Gateway 🌐

In this discussion, we delve into the implementation of a solution to centralize outbound internet traffic across multiple Virtual Private Clouds (VPCs) without compromising VPC isolation. Leveraging the capabilities of AWS Transit Gateway, you can establish a streamlined configuration, utilizing a single VPC equipped with multiple NAT gateways. This approach facilitates the consolidation of outbound traffic for numerous VPCs, all while maintaining robust VPC-to-VPC isolation through the use of multiple route tables within the transit gateway. The resulting hub-and-spoke design empowers you to efficiently oversee all outbound internet communication from a centralized and secure location.

### Traditional Challenges 🔄

In the absence of AWS Transit Gateway, the traditional approach involves combining an internet gateway with NAT gateways or NAT instances for each VPC requiring outbound internet access. However, as the number of VPCs grows, the management of multiple internet gateways, NAT gateways, and instances becomes labor-intensive and cost-inefficient. In such scenarios, AWS Transit Gateway offers a viable solution, allowing you to consolidate outbound traffic efficiently.

### Enhanced Flexibility with Security Appliances 🛡️

Moreover, while this centralized architecture is designed around a pair of shared NAT gateways, it is adaptable to incorporate other security appliances. These appliances can perform tasks such as traffic capture, policy enforcement, web filtering, provided they possess the necessary capabilities to fulfill the NAT function and adhere to required route configurations. This flexibility enables the modification of the architecture to align with specific security and compliance requirements.

![Project-Architecture](./Project-Images/Single-IGW.png | width=70%)

## Creating and Configuring the VPCs

1. Create the following three VPCs: Egress-VPC, App1-VPC, and App2-VPC. Provide values for each, as shown in the following table. For more information, see [Getting Started with Amazon VPC](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/GetStarted.html).
   
   | VPC name     | Tag       | IPv4 CIDR       | IPv6 CIDR       | Tenancy  |
   | ------------ | --------- | --------------- | --------------- | -------- |
   | Egress-VPC   |           | 192.168.0.0/16  | No IPv6 CIDR Block | Default  |
   | App1-VPC     |           | 10.0.0.0/16     | No IPv6 CIDR Block | Default  |
   | App2-VPC     |           | 10.1.0.0/16     | No IPv6 CIDR Block | Default  |


## Create Subnets in Each VPCs

2. Create the subnets in each of the VPCs as described in the following table. In the next steps, you configure the route tables to make some of these subnets public.



## Configuring Internet and Route Tables 🌐🚀

3. Create and attach an internet gateway to the VPC Egress-VPC. Use IGW as the Name tag for this internet gateway.

4. Create a NAT gateway in the VPC Egress-VPC. Create only one NAT gateway for this example. In your production environment, you should create a NAT gateway for every Availability Zone in which you have a subnet and use the NAT gateway in the same Availability zone. For Subnet, enter Egress-Public-AZ1. For Elastic IP Allocation ID, choose Create new EIP.

5. Create two new route tables in Egress-VPC. For Name tags, use Egress-Public-RT and Egress-Private-RT.

6. Add a new default route in the route table Egress-Public-RT, with the destination set to 0.0.0.0/0. Associate the route with the internet gateway IGW. Then edit the subnet association and add both the Egress-Public-AZ1 and Egress-Public-AZ2 subnets to this route table.

7. Add a new default route in the route table Egress-Private-RT, with the destination 0.0.0.0/0. Associate the route with the NAT gateway. Then edit the subnet association, adding both the Egress-Private-AZ1 and Egress-Private-AZ2 subnets to this route table.