# Configuring an Internal Load Balancer

## Task 1. Configure internal traffic and health check firewall rules.

Configure firewall rules to allow internal traffic connectivity from sources in the 10.10.0.0/16 range. This rule allows incoming traffic from any client located in the subnet.

Health checks determine which instances of a load balancer can receive new connections. For HTTP load balancing, the health check probes to your load-balanced instances come from addresses in the ranges 130.211.0.0/22 and 35.191.0.0/16. Your firewall rules must allow these connections.

### Explore the my-internal-app network

The network my-internal-app with subnet-a and subnet-b and firewall rules for RDP, SSH, and ICMP traffic have been configured for you.

In the Cloud Console, on the Navigation menu (Navigation menu), click VPC network > VPC networks. Notice the my-internal-app network with its subnets: subnet-a and subnet-b.

Each Google Cloud project starts with the default network. In addition, the my-internal-app network has been created for you as part of your network diagram.

You will create the managed instance groups in subnet-a and subnet-b. Both subnets are in the us-central1 region because an internal load balancer is a regional service. The managed instance groups will be in different zones, making your service immune to zonal failures.

### Create the firewall rule to allow traffic from any sources in the 10.10.0.0/16 range

Create a firewall rule to allow traffic in the 10.10.0.0/16 subnet.

On the Navigation menu (Navigation menu), click VPC network > Firewall. Notice the app-allow-icmp and app-allow-ssh-rdp firewall rules.

These firewall rules have been created for you.

Click Create Firewall Rule.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Name	fw-allow-lb-access

Network	my-internal-app

Targets	Specified target tags

Target tags	backend-service

Source filter	IP ranges

Source IP ranges	10.10.0.0/16

Protocols and ports	Allow all

Click create.

### Create the health check rule

Create a firewall rule to allow health checks.

On the Navigation menu (Navigation menu), click VPC network > Firewall.

Click Create Firewall Rule.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Name	fw-allow-health-checks

Network	my-internal-app

Targets	Specified target tags

Target tags	backend-service

Source filter	IP Ranges

Source IP ranges	130.211.0.0/22 and 35.191.0.0/16

Protocols and ports	Specified protocols and ports

Make sure to include the /22 and /16 in the Source IP ranges.

For tcp, specify port 80.

Click Create.

## Task 2: Create a NAT configuration using Cloud Router

The Google Cloud VM backend instances that you setup in Task 3 will not be configured with external IP addresses.

Instead, you will setup the Cloud NAT service to allow these VM instances to send outbound traffic only through the Cloud NAT, and receive inbound traffic through the load balancer.

### Create the Cloud Router instance

In the Cloud Console, on the Navigation menu (Navigation menu), click Network services > Cloud NAT.

Click Get started.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Gateway name	nat-config

VPC network	my-internal-app

Region	us-central1

Click Cloud Router, and select Create new router.

For Name, type nat-router-us-central1.

Click Create.

In Create a NAT gateway, click Create.

Wait until the NAT Gateway Status changes to Running before moving onto the next task.

## Task 3. Configure instance templates and create instance groups

A managed instance group uses an instance template to create a group of identical instances. Use these to create the backends of the internal load balancer.

## Configure the instance templates

An instance template is an API resource that you can use to create VM instances and managed instance groups. Instance templates define the machine type, boot disk image, subnet, labels, and other instance properties. Create an instance template for both subnets of the my-internal-app network.

On the Navigation menu (Navigation menu), click Compute Engine > Instance templates.

Click Create instance template.

For Name, type instance-template-1

Under Machine configuration, For Series, Select N1.

Machine type f1-micro(1 vCPU).

Click Management, security, disks, networking, sole tenancy.

Click Management.

Under Metadata, specify the following:

Key	Value

startup-script-url	gs://cloud-training/gcpnet/ilb/startup.sh

The startup-script-url specifies a script that is executed when instances are started. This script installs Apache and changes the welcome page to include the client IP and the name, region, and zone of the VM instance. You can explore this script here.

```
#! /bin/bash

apt-get update 
apt-get install -y apache2 php
apt-get install -y wget
cd /var/www/html
rm index.html -f
rm index.php -f
wget https://storage.googleapis.com/cloud-training/gcpnet/ilb/index.php
META_REGION_STRING=$(curl "http://metadata.google.internal/computeMetadata/v1/instance/zone" -H "Metadata-Flavor: Google")
REGION=`echo "$META_REGION_STRING" | awk -F/ '{print $4}'`
sed -i "s|region-here|$REGION|" index.php
```

Click Networking.

For Network interfaces, specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Network	my-internal-app

Subnet	subnet-a

Network tags	backend-service

External IP	None

The network tag backend-service ensures that the firewall rule to allow traffic from any sources in the 10.10.0.0/16 subnet and the Health Check firewall rule applies to these instances.

Click Create. Wait for the instance template to be created.

Create another instance template for subnet-b by copying instance-template-1:

Select the instance-template-1 and click Copy.

Click Management, security, disks, networking, sole tenancy.

Click Networking.

For Network interfaces, select subnet-b as the Subnet.

Click Create.

### Create the managed instance groups

Create a managed instance group in subnet-a (us-central1-a) and subnet-b (us-central1-b).

On the Navigation menu (Navigation menu), click Compute Engine > Instance groups.

Click Create Instance group.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Name	instance-group-1

Location	Single zone

Region	us-central1

Zone	us-central1-a

Instance template	instance-template-1

Autoscaling > metrics type (Click the pencil edit icon)	CPU utilization

Target CPU utilization	80, click Done.

Cool-down period	45

Minimum number of instances	1

Maximum number of instances	5

Managed instance groups offer autoscaling capabilities that allow you to automatically add or remove instances from a managed instance group based on increases or decreases in load. Autoscaling helps your applications gracefully handle increases in traffic and reduces cost when the need for resources is lower. Just define the autoscaling policy, and the autoscaler performs automatic scaling based on the measured load.

Click Create.

Repeat the same procedure for instance-group-2 in us-central1-b:

Click Create Instance group.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Name	instance-group-2

Location	Single zone

Region	us-central1

Zone	us-central1-b

Instance template	instance-template-2

Autoscaling > metric type (Click the pencil edit icon)	CPU utilization

Target CPU utilization	80, click Done.

Cool-down period	45

Minimum number of instances	1

Maximum number of instances	5

Click Create.

## Verify the backends

Verify that VM instances are being created in both subnets and create a utility VM to access the backends' HTTP sites.

On the Navigation menu, click Compute Engine > VM instances. Notice two instances that start with instance-group-1 and instance-group-2.

These instances are in separate zones, and their internal IP addresses are part of the subnet-a and subnet-b CIDR blocks.

Click Create Instance.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Name	utility-vm

Region	us-central1

Zone	us-central1-f

Series	N1

Machine type	f1-micro (1 vCPU)

Boot disk	Debian GNU/Linux 10 (buster)

Click Management, security, disks, networking, sole tenancy.

Click Networking.

For Network interfaces, click the pencil icon to edit.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Network	my-internal-app

Subnetwork	subnet-a

Primary internal IP	Ephemeral (Custom)

Custom ephemeral IP address	10.10.20.50

External IP	None

Click Done.

Click Create.

Note that the internal IP addresses for the backends are 10.10.20.2 and 10.10.30.2.

If these IP addresses are different, replace them in the two curl commands below.

For utility-vm, click SSH to launch a terminal and connect. If you see the Connection via Cloud Identity-Aware Proxy Failed popup, click Retry.

To verify the welcome page for instance-group-1-xxxx, run the following command:

```
curl 10.10.20.2
```

The output should look like this (do not copy; this is example output):

```
<h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
 instance-group-1-1zn8<h2>Server Location</h2>Region and Zone: us-central1-a
```

To verify the welcome page for instance-group-2-xxxx, run the following command:

```
curl 10.10.30.2
```

The output should look like this (do not copy; this is example output):

```
<h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
 instance-group-2-q5wp<h2>Server Location</h2>Region and Zone: us-central1-b
```

This will be useful when verifying that the internal load balancer sends traffic to both backends.

Close the SSH terminal to utility-vm

## Task 4. Configure the internal load balancer

Configure the internal load balancer to balance traffic between the two backends (instance-group-1 in us-central1-a and instance-group-2 in us-central1-b)

### Start the configuration

In the Cloud Console, on the Navigation menu (Navigation menu), click Network Services > Load balancing.
Click Create load balancer.
Under TCP Load Balancing, click Start configuration.
For Internet facing or internal only, select Only between my VMs.
Choosing Only between my VMs makes this load balancer internal. This choice requires the backends to be in a single region (us-central1) and does not allow offloading TCP processing to the load balancer.

Click Continue.

For Name, type my-ilb.

### Configure the regional backend service

The backend service monitors instance groups and prevents them from exceeding configured usage.

Click Backend configuration.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (select option as specified)

Region	us-central1

Network	my-internal-app

Instance group	instance-group-1 (us-central1-a)

Click Done.

Click Add backend.

For Instance group, select instance-group-2 (us-central1-b).

Click Done.

For Health Check, select Create a health check.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (select option as specified)

Name	my-ilb-health-check

Protocol	TCP

Port	80

Check interval	10 sec

Timeout	5 sec

Healthy threshold	2

Unhealthy threshold	3

Health checks determine which instances can receive new connections. This HTTP health check polls instances every 10 seconds, waits up to 5 seconds for a response, and treats 2 successful or 3 failed attempts as healthy threshold or unhealthy threshold, respectively.

Click Save and continue.

Verify that there is a blue check mark next to Backend configuration in the Cloud Console. If there isn't, double-check that you have completed all the steps above.

### Configure the frontend

The frontend forwards traffic to the backend.

Click Frontend configuration.

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Subnetwork	subnet-b

Internal IP > IP address	Reserve static internal IP address

Specify the following, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)

Name	my-ilb-ip

Static IP address	Let me choose

Custom IP address	10.10.30.5

Click Reserve.

For Ports, type 80.

Click Done.

### Review and create the internal load balancer

Click Review and finalize.
Review the Backend and Frontend.
Click Create. Wait for the load balancer to be created before moving to the next task.

## Task 5. Test the internal load balancer

Verify that the my-ilb IP address forwards traffic to instance-group-1 in us-central1-a and instance-group-2 in us-central1-b.

Access the internal load balancer
On the Navigation menu, click Compute Engine > VM instances.

For utility-vm, click SSH to launch a terminal and connect.

To verify that the internal load balancer forwards traffic, run the following command:

```
curl 10.10.30.5
```

The output should look like this (do not copy; this is example output):

```
<h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
 instance-group-2-1zn8<h2>Server Location</h2>Region and Zone: us-central1-b
```

As expected, traffic is forwarded from the internal load balancer (10.10.30.5) to the backend.

Run the same command a couple of times
