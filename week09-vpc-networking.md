# Week 9: VPC Networking and Troubleshooting

## HarborTech Ticket Summary
The client reported that users cannot reach the web application even though the EC2 instance is running. The application uses HTTP port 80.

## Client Impact
Users cannot access the website. This can interrupt daily work and business operations.

## AWS Services Involved
- Amazon VPC
- Public and private subnets
- Route tables
- Internet gateway
- NAT gateway
- Security groups
- Network ACLs
- EC2 instances

## Virtualization Connection
AWS uses virtualization to create cloud servers and networks. VPCs, subnets, route tables, and security groups help control how traffic moves between resources.

## Evidence Reviewed
The simulated evidence packet shows:
- VPC: vpc-0a91
- VPC CIDR: 10.42.0.0/16
- EC2 instance is running.
- Public IPv4: 198.51.100.24
- Web server is listening on TCP port 80.
- Security group allows inbound TCP 80 from 0.0.0.0/0.
- Network ACL summary allows HTTP traffic and return traffic.
- Internet gateway is attached.
- Route table rtb-0a91 is associated with subnet-0a91.
- The route table only shows 10.42.0.0/16 (local).
- Complete ACL rules and connectivity test results were not provided.

## Operational Analysis
Traffic should travel from the internet through the internet gateway to the EC2 web server.

The first unsupported assumption is that the subnet is public and reachable from the internet. Having a public IP and an attached internet gateway does not guarantee connectivity.

The most likely problem is the missing default route (0.0.0.0/0) to the internet gateway. The security group allows HTTP traffic, but it cannot fix a missing route.

## Recommendation
With approval, I recommend adding the missing default route (0.0.0.0/0) to the internet gateway in the correct public route table.

After the change, I would test the website using HTTP port 80 and check whether it responds. I would also verify the complete network ACL rules and route table association.

For a private server that only needs to download updates, I would recommend a NAT gateway instead of direct internet access.

## Escalation Notes
Any production changes to routes, gateways, security groups, network ACLs, or network design require approval. If the problem continues after verification, I would contact my supervisor.

## Lessons Learned
I learned that an EC2 instance can be running while users cannot reach its website. Checking the full network path helps find the problem without changing unnecessary settings.

## Professional Vocabulary
- **VPC:** A private virtual network in AWS.
- **Subnet:** A smaller network inside a VPC.
- **Route Table:** Rules that decide where network traffic goes.
- **Internet Gateway:** Connects a VPC to the internet.
- **NAT Gateway:** Allows private resources to start outbound internet connections without accepting unsolicited inbound connections.
- **Security Group:** Controls allowed traffic for AWS resources.
- **Network ACL:** Controls traffic entering and leaving a subnet.
- **CIDR:** A way to describe a range of IP addresses.
- **Public Subnet:** A subnet with a route to an internet gateway.
- **Private Subnet:** A subnet without a direct route to an internet gateway.
