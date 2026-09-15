# Week 2: IAM and AWS CLI Investigation

## HarborTech Ticket Summary

Marcus can sign in to AWS, but he cannot access the riverside-inventory bucket. His account does not have the permissions needed for his job.

## Client Impact

Marcus cannot view, read, or upload inventory files. This stops him from completing his work.

## AWS Services Involved

* IAM
* Amazon S3
* AWS CLI
* CloudShell
* AWS Regions

## Virtualization Connection

IAM controls who can use cloud storage, servers, and networks. This helps protect cloud resources from unauthorized access.

## Evidence Reviewed

Marcus signed in successfully but received AccessDenied when he tried to list the bucket. No group or policy was listed for his account.

CloudShell commands confirmed the caller identity and showed information about LabRole. LabRole had seven managed policies and no inline policies. The IAM page showed the same results.

## Operational Analysis

Marcus’s sign-in proves that authentication worked. AccessDenied shows that he does not have permission to complete the action. This is an authorization problem, not a broken account.

## Recommendation

Marcus should not receive AmazonS3FullAccess because it gives too much access. He only needs `ListBucket`, `GetObject`, and `PutObject` for the riverside-inventory bucket.

## Escalation Notes

An authorized HarborTech team member should review and make the permission change. The intern should only document and report the issue.

## Lessons Learned

Authentication confirms who a user is. Authorization controls what the user can do. Least privilege means giving only the access needed for the job.

## Professional Vocabulary

* **Authentication:** Confirms a user’s identity.
* **Authorization:** Controls what a user can do.
* **IAM:** Manages AWS users and access.
* **Policy:** Lists allowed or denied actions.
* **Least Privilege:** Gives only needed access.
* **AccessDenied:** The action is not allowed.
* **AWS CLI:** Runs AWS commands.
* **CloudShell:** A browser based command terminal.
* **Caller Identity:** The AWS role and account being used.
* **Resource Scope:** The resources a permission covers.
