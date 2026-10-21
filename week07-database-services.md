# Week 7: Managed Database Services

## HarborTech Ticket Summary
HarborTech is helping Mom & Pop Café improve its MySQL database. The café currently uses MySQL on EC2, which requires the team to manage backups, server updates, and maintenance.

## Client Impact
Database problems could prevent customers from placing orders or viewing their order history. A migration mistake could also cause data loss or downtime.

## AWS Services Involved
- Amazon EC2: Runs the café application and current database environment.
- Amazon RDS: Provides managed relational databases.
- Amazon Aurora: A managed database option compatible with MySQL.
- Amazon DynamoDB: A NoSQL database service.
- Multi-AZ: Helps improve database availability.
- AWS Database Migration Service: Helps move databases between systems.
- Security Groups: Control network access to AWS resources.

## Virtualization Connection
EC2 provides virtual servers where applications and databases can run. Amazon RDS reduces the need to manage the database server and operating system directly.

## Evidence Reviewed
- AWS CLI confirmed access to the lab account.
- The café application was running on EC2.
- The VPC network was `10.200.0.0/20`.
- A database security group was created.
- TCP port 3306 was allowed from the café application's security group.
- The café website successfully displayed a test order totaling $11.00.
- The RDS console showed no database instances when inspected.

## AWS Academy Activity: Actions and Results
1. Connected to the CLI Host using SSH.
2. Ran AWS CLI commands to inspect the café's EC2 instance and network.
3. Created a security group for the café database.
4. Allowed MySQL traffic on port 3306 from the application security group.
5. Verified the security group settings using AWS CLI.
6. Opened the café website and confirmed a test order appeared in Order History.

The available evidence does not confirm a completed database migration.

## Operational Analysis
The café uses a relational MySQL database with SQL queries and transactions. Amazon RDS for MySQL is a suitable option because it can reduce server maintenance and provide managed backup features.

DynamoDB would not be a direct replacement because it uses a different data model. Aurora should be considered after comparing compatibility, performance, availability, and costs.

## Recommendation
I recommend evaluating Amazon RDS for MySQL. HarborTech should review application compatibility, database backups, recovery requirements, migration risks, and costs before making changes.

AWS would manage more of the database infrastructure, but HarborTech would still be responsible for data protection, database access, and security configuration.

## Escalation Notes
A HarborTech supervisor must approve the database migration and any production changes. Database connectivity, migration compatibility, and successful data transfer still need verification.

## Lessons Learned
I learned that choosing a database depends on the application's needs. Managed databases can reduce maintenance, but they do not remove all security and operational responsibilities.

## Professional Vocabulary
- **Relational Database:** Stores structured data in related tables.
- **NoSQL Database:** Stores data without requiring a traditional relational structure.
- **Amazon RDS:** AWS managed relational database service.
- **Amazon Aurora:** AWS managed relational database engine.
- **Multi-AZ:** Uses multiple Availability Zones to improve availability.
- **Database Migration Service:** Helps migrate databases.
- **Schema Conversion:** Changes database structures for compatibility.
- **Security Group:** Controls allowed network traffic.
- **Managed Service:** A service where AWS handles some infrastructure tasks.
- **Workload Fit:** Choosing a service based on application requirements.
