# Week 3: Systems Manager and S3

## HarborTech Ticket Summary
Bright Path Nonprofits has five EC2 instances that need regular software updates. The manual process takes about 90 minutes every Monday. They also need a simple public resource page without using another EC2 server.

## Client Impact
Doing the same work manually on five servers takes time and can cause mistakes or inconsistent updates. Automation can make the work faster and more consistent.

## AWS Services Involved
- AWS Systems Manager
- Run Command
- Session Manager
- Inventory
- Parameter Store
- Amazon S3
- S3 Static Website Hosting
- AWS CloudShell and AWS CLI

## Virtualization Connection
Systems Manager can manage EC2 virtual machines from one place. S3 can host static files without needing another EC2 server.

## Evidence Reviewed
The ticket showed that five EC2 instances need repeated maintenance every Monday. I also used the AWS Learner Lab to create and test an S3 static website. I created the bucket brightpath-raian-2026 in us-east-1 and uploaded index.html. Static website hosting was enabled.

The website endpoint was:
http://brightpath-raian-2026.s3-website-us-east-1.amazonaws.com

When I tested the endpoint, I received 403 Forbidden - AccessDenied because public access was restricted.

I also updated the site from CloudShell using:

aws s3 sync ./brightpath-site s3://brightpath-raian-2026/

The output confirmed that index.html was uploaded to the S3 bucket.

## Operational Analysis
The repeated maintenance on the five EC2 instances is a good fit for centralized management and automation. Dana should not need to log in to every server separately for the same repeated task. Run Command can run commands across managed instances.

The public resource page contains static content, so Amazon S3 is a better fit than creating another EC2 server just to host the page.

## Recommendation
I recommend AWS Systems Manager Run Command for the repeated EC2 maintenance. The EC2 instances must be configured as Systems Manager managed instances with the required SSM Agent, IAM permissions, and connectivity.

I recommend Amazon S3 static website hosting for the Bright Path resource page because the page contains static HTML and does not need a separate server.

## Escalation Notes
The S3 website endpoint returned 403 Forbidden - AccessDenied because the Learner Lab restricted the required public access. I documented the restriction and did not try to bypass the Learner Lab controls. In a normal AWS environment, the required public-read access would need to be configured and approved.

## Lessons Learned
I learned that repeated server work can be managed with AWS Systems Manager instead of doing the same task manually on each server. I also learned that S3 can host a simple static website without another EC2 server. Automation should be used when it makes work easier, consistent, and easier to verify.

## Professional Vocabulary
- Systems Manager: An AWS service used to manage AWS resources from one place.
- Managed Node: A machine configured so Systems Manager can manage it.
- Run Command: A Systems Manager feature that runs commands on managed machines.
- Session Manager: A feature used to securely connect to and manage a machine.
- Inventory: A feature that collects information about managed machines.
- Parameter Store: A place to securely store configuration values and parameters.
- Automation: Using technology to perform repeated tasks with less manual work.
- Static Website Hosting: Hosting HTML, images, and other static files without a traditional web server.
- Object Storage: A way to store files as objects, such as in Amazon S3.
- Management Plane: The tools and services used to control and manage cloud resources.
