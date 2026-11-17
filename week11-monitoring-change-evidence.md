# Week 11: Monitoring and Change Evidence

## HarborTech Ticket Summary
Riverside Goods reported that its website became slow after an unexpected change. HarborTech investigated the issue using AWS monitoring and audit records to find out what happened.

## Client Impact
The website became slower, which could affect customers. Without monitoring and alerts, the company might not notice problems until customers complain.

## AWS Services Involved
We used Amazon CloudWatch to monitor performance, CloudWatch Logs to check application activity, CloudWatch Alarms to identify problems, AWS CloudTrail to review changes, and AWS Config to compare settings before and after the change.

## Virtualization Connection
AWS uses virtual servers that can be managed and changed through software. Monitoring and audit records help us understand how those changes affect system performance.

## Evidence Reviewed
- CloudTrail recorded an Auto Scaling change at 14:05:12.
- AWS Config showed capacity changed from 3 to 1 at 14:06:03.
- CloudWatch showed response time increased from 0.3 to 2.4 seconds at 14:07.
- Application logs showed only one healthy target at 14:07:30.
- The CloudWatch alarm changed to ALARM at 14:09.

## Operational Analysis
The records show that the Auto Scaling capacity was reduced before the website slowed down. CloudTrail identified an assumed-role session and source IP address 203.0.113.24. AWS Config confirmed the change. The timing suggests that reducing capacity contributed to the slowdown, but further investigation is needed to confirm the cause.

## Recommendation
A senior administrator should check whether the capacity change was approved. If needed, they should review restoring the previous settings. The team should continue monitoring the website.

## Escalation Notes
The change has no approved request on file. I would report the findings to my supervisor and ask a senior administrator to investigate before making production changes.

## Lessons Learned
I learned that CloudWatch, CloudTrail, and AWS Config provide different types of evidence. Using them together helps explain what changed, when it happened, and how it affected the system.

## Professional Vocabulary
1. **Monitoring:** Watching a system to check its performance.
2. **Metric:** A measurement of system activity.
3. **Log:** A record of system events.
4. **Alarm:** A warning when a condition is met.
5. **CloudWatch:** An AWS service used to monitor systems.
6. **CloudTrail:** An AWS service that records account activity.
7. **AWS Config:** An AWS service that tracks resource settings.
8. **Audit Trail:** A record of actions and changes.
9. **Configuration History:** A record of how settings changed over time.
10. **Incident Timeline:** A list of events in the order they happened.
11. **Evidence Correlation:** Comparing different records to understand an incident.
