# Week 4: EC2 Evidence Lab

## HarborTech Ticket Summary
Riverside Goods reported that its EC2 web server showed a Running state, but users could not reach the web page. The goal was to use evidence to find the problem, make the smallest supported correction, verify the result, test the instance lifecycle, and clean up the temporary resources.

## Client Impact
The web server was running, but users could not access the application through HTTP. A curl test to the public IPv4 address timed out.

## Environment and Resource Names
- AWS Region: us-east-1
- VPC: vpc-075d35c8a9343da7c
- Subnet: subnet-02d4e813acfb307a5
- Availability Zone: us-east-1c
- Instance ID: i-018921158cb342659
- AMI ID: ami-0b245cc5f82576748
- Instance type: t2.micro
- Security group: sg-0fce9572383829cab
- Initial public IPv4: 54.210.180.32
- Public IPv4 after stop/start: 54.167.99.144

## AWS Documentation Evidence

### Security Groups
AWS document: Amazon EC2 security groups for your EC2 instances  
PDF page: 3296  
Quote: “Inbound rules control the incoming traffic to your instance”  
URL: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html

This applied to the lab because the security group initially had no inbound rules. After TCP port 80 was allowed, the web page became reachable.

### User Data
AWS document: Run commands when you launch an EC2 instance with user data input  
PDF page: 1740  
Quote: “By default, user data scripts and cloud-init directives run only during the boot cycle when you first launch an instance.”  
URL: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html

User data installed and started Apache and created the test page. User data alone did not prove that Apache was currently running, so the service was also checked from inside the instance.

### EC2 Lifecycle
AWS document: What happens when you stop an instance  
PDF page: 1554  
Quote: “The public IPv4 address that Amazon EC2 automatically assigned to the instance upon launch or start.”  
URL: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/how-ec2-instance-stop-start-works.html

This matched the lab because the automatically assigned public IPv4 address changed after the instance was stopped and started.

## CloudShell Command Record

The instance was checked after launch:

```bash
aws ec2 describe-instances \
  --instance-ids i-018921158cb342659 \
  --query 'Reservations[0].Instances[0].{InstanceId:InstanceId,State:State.Name,PublicIPv4:PublicIpAddress,AMI:ImageId}' \
  --output table
```

The output showed:
- State: running
- Public IPv4: 54.210.180.32
- AMI: ami-0b245cc5f82576748

Status checks were inspected:

```bash
aws ec2 describe-instance-status \
  --instance-ids i-018921158cb342659 \
  --query 'InstanceStatuses[0].{InstanceState:InstanceState.Name,InstanceCheck:InstanceStatus.Status,SystemCheck:SystemStatus.Status}' \
  --output table
```

The instance check and system check both returned `ok`.

The security group was inspected:

```bash
aws ec2 describe-security-groups \
  --group-ids sg-0fce9572383829cab \
  --query 'SecurityGroups[0].{GroupId:GroupId,InboundRules:IpPermissions}' \
  --output json
```

The result showed:

```text
"InboundRules": []
```

The initial HTTP test failed:

```bash
curl --connect-timeout 10 http://54.210.180.32
```

Result:

```text
curl: (28) Connection timed out after 10001 milliseconds
```

## Baseline Evidence
The instance was running, had a public IPv4 address, and both EC2 status checks were OK. These results showed that the instance was available from the EC2 control-plane view, but they did not prove that the web application was reachable.

The security group had no inbound rules, and the public HTTP test timed out. The timeout alone did not prove the exact cause, but the evidence made the missing inbound HTTP rule the strongest supported cause.

## Root-Cause Analysis
The strongest supported root cause was the missing inbound TCP port 80 rule in the security group. The instance itself was running and passed its status checks, but the security group did not allow incoming HTTP traffic.

Rebuilding the EC2 instance was not justified because the evidence did not show an instance failure.

## Corrective Action
The smallest supported change was to allow inbound TCP port 80:

```bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-0fce9572383829cab \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0
```

The command returned `Return: true`.

The security group was checked again and showed TCP port 80 allowed.

## Verification Evidence
After the security group change, the public HTTP test was repeated:

```bash
curl http://54.210.180.32
```

Result:

```html
<h1>Riverside Goods Week 4 EC2 Lab</h1>
```

This verified that the web page was reachable after the security group correction.

## IMDSv2 and Guest Evidence
Session Manager was used to check the EC2 instance from inside the guest operating system.

```bash
systemctl status httpd --no-pager
```

Apache showed:

```text
Active: active (running)
```

The local web page was tested:

```bash
curl http://localhost
```

Result:

```html
<h1>Riverside Goods Week 4 EC2 Lab</h1>
```

IMDSv2 was then used:

```bash
TOKEN=$(curl -sS -X PUT \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600" \
  http://169.254.169.254/latest/api/token)

curl -sS \
  -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/instance-id
```

Result:

```text
i-018921158cb342659
```

The guest evidence showed that Apache and the page worked inside the instance. This answered a different question from the AWS CLI checks, which showed the AWS resource state.

## Stop/Start Lifecycle Test
Before stopping the instance:
- Instance ID: i-018921158cb342659
- Public IPv4: 54.210.180.32
- Web page: working

The instance was stopped and started:

```bash
aws ec2 stop-instances --instance-ids i-018921158cb342659
aws ec2 wait instance-stopped --instance-ids i-018921158cb342659
aws ec2 start-instances --instance-ids i-018921158cb342659
aws ec2 wait instance-running --instance-ids i-018921158cb342659
```

After restart:
- Instance ID: i-018921158cb342659
- Public IPv4: 54.167.99.144
- Web page: working

The instance identity and web content persisted, while the automatically assigned public IPv4 address changed. This was consistent with the AWS lifecycle documentation cited above.

## Cleanup Evidence
The disposable EC2 instance was terminated:

```bash
aws ec2 terminate-instances --instance-ids i-018921158cb342659
aws ec2 wait instance-terminated --instance-ids i-018921158cb342659
```

The temporary security group was deleted:

```bash
aws ec2 delete-security-group --group-id sg-0fce9572383829cab
```

The command returned:

```text
"Return": true
```

A final describe command returned `InvalidGroup.NotFound`, confirming that the temporary security group no longer existed.

## Escalation and Change-Control Notes
For a production system, I would not open TCP port 80 to `0.0.0.0/0` without authorization. This permits HTTP connections from any IPv4 address and may expose the application more broadly than intended.

Before making the change in production, I would confirm the required source range and get approval from the system owner or authorized security/network team. I would also record the original security group rules so the change could be rolled back if necessary.

## Lessons Learned
A Running EC2 state does not prove that an application is reachable. Troubleshooting should check each layer with evidence. In this lab, the instance and Apache were healthy, but the security group blocked incoming HTTP traffic. Making one small supported change fixed the problem without rebuilding the server.

## Professional Vocabulary
- **Security group:** A virtual firewall that controls allowed inbound and outbound traffic.
- **Status checks:** AWS checks used to identify instance or underlying system problems.
- **User data:** Commands or scripts that can configure an EC2 instance during launch.
- **IMDSv2:** The Instance Metadata Service used from inside an EC2 instance with token-based requests.
- **Root cause:** The strongest evidence-supported reason for a problem.
- **Corrective action:** A change made to fix the identified problem.
- **Verification:** Evidence showing that the corrective action worked.
- **Lifecycle:** The states and changes an EC2 instance goes through, such as running, stopped, and terminated.
- **Rollback:** Returning a system to its previous configuration if a change causes a problem.
- **Least change:** Fixing only the supported problem instead of making unnecessary changes.
