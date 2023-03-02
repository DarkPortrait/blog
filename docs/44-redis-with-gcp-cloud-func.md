The official documentation to connect Redis with cloud functions can be found [here](https://cloud.google.com/memorystore/docs/redis/connect-redis-instance-functions#python_2)

Setting up Redis with GCP Cloud Functions require the following steps:

1. Set up local environment GCP permission 
1. Create redis instance 
1. Create a VPC network
1. Create a subnet 
1. Create a serverless VPC Access Connector
1. Deploy the function with the connector access

## Step 1: Set up the local environment GCP permission

Set up the variable in terminal:

```bash
export GOOGLE_APPLICATION_CREDENTIALS='/path/to/your/client_secret.json'
```

This will not enable VPC connection from local environment. #ToDo

## Step 2: Redis instance creation

In GCP, the product is called Memorystore for Redis. It is a serverless Redis service.

Documentation is [here](https://cloud.google.com/memorystore/docs/redis/create-instance-gcloud)

gcloud command:

```bash
gcloud redis instances create br-pixel-redis \                                                    
--size=1 \
--region us-central1 \
--redis-version redis_6_x
```

Save the configuration of the redis instance for using in the function.

```bash
gcloud redis instances describe br-pixel-redis --region us-central1 > redis-function/redis-conf.yaml
```

It will be something like this:

```
authorizedNetwork: projects/my-project/global/networks/default
createTime: '2018-04-09T21:47:56.824081Z'
currentLocationId: us-central1-a
host: 10.0.0.27
locationId: us-central1-a
memorySizeGb: 2
name: projects/my-project/locations/us-central1/instances/myinstance
networkThroughputGbps: 2
port: 6379
redisVersion: REDIS_6_X
reservedIpRange: 10.0.0.24/29
state: READY
tier: BASIC
```

We will be using the `host` and `port` to connect with the instance.

## Step 3: Create a VPC network

Virtual Private Cloud (VPC) networks allow for secured connection between resources within GCP.

I have used the `default` VPC network of the GCP project for avoiding firewall configurations. 

Should we need to create one, [here](https://cloud.google.com/vpc/docs/create-modify-vpc-networks#creating_networks) is the documentation to create a new VPC network.

## Step 4: Create a subnet for the network

A network must have at least one subnet before you can use it. Auto mode VPC networks create subnets in each region automatically. Custom mode VPC networks start with no subnets, giving you full control over subnet creation.

However, we have create a new one because subnets used for VPC connectors must have a netmask of 28.

[Here](https://cloud.google.com/vpc/docs/create-modify-vpc-networks#add-subnets) is the documentation to add a subnet.

```bash
gcloud compute networks subnets create br-pixel-cache-subnet \                      
--network default \
--range 10.0.0.0/28 \
--region us-central1
```

Valid network ranges can be found [here](https://cloud.google.com/vpc/docs/subnets#manually_created_subnet_ip_ranges)

## Step 5: Create a serverless VPC Connector

Serverless VPC access enables direct connection to the Virtual Private Cloud network from serverless environments such as Cloud Run, Cloud Functions or App Engine. Documentation is [here](https://cloud.google.com/vpc/docs/serverless-vpc-access)

```bash
gcloud compute networks vpc-access connectors create br-pixel-cache-vpc \               
--region us-central1 \
--subnet br-pixel-cache-subnet
```

## Step 6: Deploy the Cloud Function

The cloud function needs to point to the Connector using the following argument in the deploy command:

```bash
gcloud functions deploy [FUNCTION_NAME] \
--runtime python37 \
--trigger-http \
--region [REGION] \
--vpc-connector projects/[PROJECT_ID]/locations/[REGION]/connectors/[CONNECTOR_NAME] \
--set-env-vars REDISHOST=[REDIS_IP],REDISPORT=[REDIS_PORT]
```

For example:

```bash
gcloud functions deploy br-pixel-test \
--entry-point=main \
--allow-unauthenticated \
--runtime=python38 \
--region=us-central1 \
--trigger-http \
--memory 256MB \
--min-instances 1 \
--vpc-connector projects/recharge-webhooks/locations/us-central1/connectors/br-pixel-cache-vpc
```
