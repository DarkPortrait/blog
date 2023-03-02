# Automating the Deployment of Infrastructure Using Deployment Manager

## Overview

Deployment Manager is an infrastructure deployment service that automates the creation and management of Google Cloud resources. Write flexible template and configuration files and use them to create deployments that have a variety of Cloud Platform services, such as Cloud Storage, Compute Engine, and Cloud SQL, configured to work together.

In this lab, you create a Deployment Manager configuration with a template to automate the deployment of Google Cloud infrastructure. Specifically, you deploy one auto mode network with a firewall rule and two VM instances

## Objectives 

Create a configuration for an auto mode network

Create a configuration for a firewall rule

Create a template for VM instances

Create and deploy a configuration

Verify the deployment of a configuration

## Task 1. Configure the network

A configuration describes all the resources you want for a single deployment.

### Verify that the Deployment Manager API is enabled

In the Cloud Console, on the Navigation menu (Navigation menu), click APIs & services > Library.

In the search bar, type Deployment Manager, and click the result for Cloud Deployment Manager V2 API.

### Start the Cloud Shell Editor

To write the configuration and the template, you use the Cloud Shell Editor.

In the Cloud Console, click Activate Cloud Shell (Cloud Shell).

If prompted, click Continue.

Run the following commands:

mkdir dminfra
cd dminfra

In Cloud Shell, click Open Editor (Cloud Shell Editor).

In the left pane of the code editor, expand the dminfra folder.

### Create the auto mode network configuration

A configuration is a file written in YAML syntax that lists each of the resources you want to create and their respective resource properties. A configuration must contain a resources: section followed by the list of resources to create. Start the configuration with the mynetwork resource.

To create a new file, click File > New File.

Name the new file config.yaml, and then open it.

Copy the following base code into config.yaml:

```
resources:
# Create the auto-mode network
- name: [RESOURCE_NAME]
  type: [RESOURCE_TYPE]
  properties:
    #RESOURCE properties go here
```

In config.yaml, replace `[RESOURCE_NAME]` with mynetwork

To get a list of all available network resource types in Google Cloud, run the following command in Cloud Shell:

```
gcloud deployment-manager types list | grep network
```
The output should look like this (do not copy; this is example output):

```
compute.beta.subnetwork
compute.alpha.subnetwork
compute.v1.subnetwork
compute.beta.network
compute.v1.network
compute.alpha.network
```

Locate compute.v1.network, which is the type needed to create a VPC network using Deployment Manager.

By definition, an auto mode network automatically creates a subnetwork in each region. 

## Task 2. Configure the firewall rule

In order to allow ingress traffic instances in mynetwork, you need to create a firewall rule.

### Add the firewall rule to the configuration

Add a firewall rule that allows HTTP, SSH, RDP, and ICMP traffic on mynetwork.

To get a list of all available firewall rule resource types in Google Cloud, run the following command in Cloud Shell:

```
gcloud deployment-manager types list | grep firewall
```

The output should look like this (do not copy; this is example output):

```
compute.v1.firewall
compute.alpha.firewall
compute.beta.firewall
```

Locate compute.v1.firewall, which is the type needed to create a firewall rule using Deployment Manager.

The final `config.yaml`

```
imports:
- path: instance-template.jinja

resources:
# Create the auto-mode network
- name: mynetwork
  type: compute.v1.network
  properties:
    autoCreateSubnetworks: true
# Create the firewall rule
- name: mynetwork-allow-http-ssh-rdp-icmp
  type: compute.v1.firewall
  properties:
    network: $(ref.mynetwork.selfLink)
    sourceRanges: ["0.0.0.0/0"]
    allowed:
    - IPProtocol: TCP
      ports: [22, 80, 3389]
    - IPProtocol: ICMP

# Create the mynet-us-vm instance
- name: mynet-us-vm
  type: instance-template.jinja
  properties:
    zone: us-central1-a
    machineType: n1-standard-1
    network: $(ref.mynetwork.selfLink)
    subnetwork: regions/us-central1/subnetworks/mynetwork

# Create the mynet-eu-vm instance
- name: mynet-eu-vm
  type: instance-template.jinja
  properties:
    zone: europe-west1-d
    machineType: n1-standard-1
    network: $(ref.mynetwork.selfLink)  
    subnetwork: regions/europe-west1/subnetworks/mynetwork
```

## Task 3. Create a template for VM instances

Deployment Manager allows you to use Python or Jinja2 templates to parameterize your configuration. This allows you to reuse common deployment paradigms such as networks, firewall rules, and VM instances.

### Create the VM instance template

Because you will be creating two similar VM instances, create a VM instance template.

To create a new file, click File > New File.

Name the new file instance-template.jinja, and then open it.

Properties to define:

machineType: Machine type and zone
zone: Instance zone
networkInterfaces: Network and subnetwork that VM is attached to
accessConfigs: Required to give the instance a public IP address (required in this lab). To create instances with only an internal IP address, remove the accessConfigs section.
disks: The boot disk, its name and image
Most properties are defined as template properties, which you will provide values for from the top-level configuration (config.yaml).

To get a list of all available instance resource types in Google Cloud, run the following command in Cloud Shell:

```
gcloud deployment-manager types list | grep instance
```

Locate compute.v1.instance, which is the type needed to create a VM instance using Deployment Manager.

Final `instance-template.jinja`:

```
 resources:
- name: {{ env["name"] }}
  type: compute.v1.instance  
  properties:
     machineType: zones/{{ properties["zone"] }}/machineTypes/{{ properties["machineType"] }}
     zone: {{ properties["zone"] }}
     networkInterfaces:
      - network: {{ properties["network"] }}
        subnetwork: {{ properties["subnetwork"] }}
        accessConfigs:
        - name: External NAT
          type: ONE_TO_ONE_NAT
     disks:
      - deviceName: {{ env["name"] }}
        type: PERSISTENT
        boot: true
        autoDelete: true
        initializeParams:
          sourceImage: https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/family/debian-9
```

## Deploy the configuration

```
gcloud deployment-manager deployments create dminfra --config=config.yaml --preview
```

```
gcloud deployment-manager deployments update dminfra
```

Or, directly deploy:
```
gcloud deployment-manager deployments create dminfra --config=config.yaml
```

Delete using:
```
gcloud deployment-manager deployments delete dminfra
```

## Task 5. Verify your deployment

Verify your network in the Cloud Console

In the Cloud Console, on the Navigation menu (Navigation menu), click VPC network > VPC networks.

View the mynetwork VPC network with a subnetwork in every region.

On the Navigation menu, click VPC network > Firewall.

Sort the firewall rules by Network.

View the mynetwork-allow-http-ssh-rdp-icmp firewall rule for mynetwork.

Verify your VM instances in the Cloud Console

On the Navigation menu (Navigation menu), click Compute Engine > VM instances.

View the mynet-us-vm and mynet-eu-vm instances.

Note the internal IP address for mynet-eu-vm.

For mynet-us-vm, click SSH to launch a terminal and connect.

To test connectivity to mynet-eu-vm's internal IP address, run the following command in the SSH terminal (replacing mynet-eu-vm's internal IP address with the value noted earlier):

```
ping -c 3 <Enter mynet-eu-vm's internal IP here>
```

This should work because both VM instances are on the same network and the firewall rule allows ICMP traffic!
