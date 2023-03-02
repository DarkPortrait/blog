## Date: April 1. 2022

### Module 1

Client server model: We are making a request and some server in the backend is fulfilling the request.

Fundamental principles of cloud computing: 
1. Services on demand
1. Avoid large upfront investment/capex
1. Provision computing resources as needed - elasticity
1. Pay for what we use

Baked in vs Bolt on

AWS has 200+ services ready to go

AWS core services categories
1. compute
1. networking an content delivery
1. storage
1. database
1. security, identity and compliance
1. monitoring and analytics

### Module 2: compute

Instance types : 
1. General purpose : t series 
1. Compute optimized : 
1. Accelerated computing : 
1. Storage optimized : high IOPS
1. Memory optimized

ASG = auto scaling group = add or removing instance

ELB = Elastic load balancer = distributes workload across several amazon EC2 instances, provide a single point of contact for the asg

Messaging services

Monolithic appliation vs micro services 
SQS = Simple Queue service
SNS = Simple notification service = sending messgaes for the specific topics they have subscribed to

AWS Lambda
AWS Containers = ECS vs EKS
AWS Fargate = serverless containers where orchestration is taken care of. higher level of abstraction than EKS. 

### Module 3: Global infrastructure and reliability

Regions consists of 2+ availability zones. 

Within the availability zones there will be multiple data centers.

Cloudfront is CDN

### Module 4: Network

Network access Control List ? = stateless
security groups are stateful and deny all inbound traffic by default
internet gateway is used to [?]
Route 53 - DNS port number is 53

Missed this session :(

### Module 5: Database and storage

Storage
* block storage 
* ec2 instances has local storage called local storage. `Ephemeral storage` 
* ebs snapshots - stores delta in data for backup
* s3 object storage with 6 classes 
* file storage - multiple clients can access same data with a shared file structure

Databases 
* relational database service - a managed service for the data service
* amazon aurora - 
	* 6 copies in 3 availability zones 
	* for high data durability
	* cut down on IOPS and reduce costs thereby
* dynamoDB - serverless key-value database
* aws database migration service 
* redshift - data warehouse for etl jobs
* documentDB - for mongoDB
* neptune - highly connected dataset
* qldb - blockchain
* managed blockchain for ledger database
* elasticache - caching layer to improve performance
* dynamoBD accelerator - improve performance 

### Module 6: Security

* Shared responsibility model
* DDoS attack prevention : AWS Shield 
* AWS Key Management Service - create and manage crypto keys 

Missed this section :(

### Module 7: Monitoring

* cloudtrail- log service, track user activity and api usage
* cloudwatch - monitor architecture, aws resources and applications, metrics from a dashbaord
* aws trusted advisor - reduce costs, improve performance, improve security. dashboard shows cost optimization, performance, security, fault tolerance, service limits - no problem green, recommendations yellow, actions red

### Module 8: Pricing and support

Pricing: pay as you go, commitment for 1-3 years,volume discount.

### Module 9: Migration and Innovation

AWS cloud adoption framework
Six areas of focus , in 2 key areas 
1. Business capabilities 
	* Business
	* People
	* Governance
1. Technical capabilities
	* Platform
	* Security
	* Operations

Six R's - migration strategies
* Rehost - lift & shift
* Replatform
* Refactor / rearchitect 
* Repurchase
* Retain
* Retire

AWS Snow Family
* Copy data using a device

Five pillars of well architected framework

### Module 10: Preparing for the certified cloud practitioner

4 domains :
1. cloud concepts
2. security and compliance
3. technology
4. billing and pricing
