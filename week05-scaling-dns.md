# Week 5: Scaling, Load Balancing, and DNS

## HarborTech Ticket Summary

Riverside Goods expects more customer traffic. The application depends on one EC2 instance and one public endpoint. HarborTech needs to prepare for more traffic and reduce the risk of one failure affecting the application.

## Client Impact

If traffic increases, one EC2 instance may not have enough capacity. The application could become slow or unavailable. Using only one instance and one endpoint also creates a single point of failure.

## Provided Ticket Evidence

HarborTech provided this evidence:
- Previous peak CPU utilization was 92%.
- Traffic is expected to double.
- Proposed Auto Scaling capacity is minimum 2, desired 2, and maximum 6.
- Both test targets were reported healthy.
- A secondary recovery endpoint is available.
- Route 53 failover is not configured yet.

This is provided ticket evidence, not evidence I collected from my AWS environment.

## AWS Commands Used

```bash
aws autoscaling describe-auto-scaling-groups \
  --query 'AutoScalingGroups[].{Name:AutoScalingGroupName,Min:MinSize,Desired:DesiredCapacity,Max:MaxSize}' \
  --output table
```

```bash
aws elbv2 describe-target-groups \
  --query 'TargetGroups[].{Name:TargetGroupName,TargetGroupArn:TargetGroupArn,Protocol:Protocol,Port:Port}' \
  --output table
```

```bash
aws route53 list-hosted-zones \
  --query 'HostedZones[].{Name:Name,Id:Id,Private:Config.PrivateZone}' \
  --output table
```

## AWS Evidence Collected

My AWS Learner Lab returned no Auto Scaling groups, no target groups, and no Route 53 hosted zones. Because no target group was returned, there was no target health to inspect.

## Virtualization Connection

Virtualization allows cloud resources to change without depending on one physical server. Auto Scaling can add or remove EC2 instances, while a load balancer can distribute traffic across healthy instances.

## Operational Analysis

The previous 92% CPU use and expected traffic increase show a capacity risk. Auto Scaling can provide more capacity when needed. Load balancing can distribute traffic instead of depending on one instance. The ticket reports healthy targets, but this does not prove DNS failover is ready. My AWS environment did not contain the resources needed to personally verify these proposed configurations.

## Recommendation

I recommend Auto Scaling and load balancing before the traffic surge. Auto Scaling can provide more capacity, and load balancing can spread traffic across healthy instances. The proposed minimum 2, desired 2, and maximum 6 allows capacity to increase when needed. Route 53 failover should be configured and tested before it is considered ready.

## Escalation Notes

Production changes to Auto Scaling, load balancing, or Route 53 require approval. More verification is also needed because these resources were not available in my AWS Learner Lab.

## Lessons Learned

Week 5 showed that scaling and availability solve different problems. Auto Scaling helps with capacity, load balancing distributes traffic, health checks identify unhealthy targets, and Route 53 can support DNS failover.

## Professional Vocabulary

- **Elasticity:** Adding or removing resources when demand changes.
- **Scalability:** The ability of a system to handle more work.
- **Load Balancer:** Distributes traffic across multiple targets.
- **Target Group:** A group of resources that can receive traffic.
- **Health Check:** Checks if a target is healthy.
- **Auto Scaling Group:** Manages the number of EC2 instances.
- **Launch Template:** Defines how new EC2 instances are launched.
- **Desired Capacity:** The number of instances the group tries to maintain.
- **Route 53:** AWS service used for DNS routing.
- **Failover:** Sending traffic to a backup when the main endpoint is unavailable.
