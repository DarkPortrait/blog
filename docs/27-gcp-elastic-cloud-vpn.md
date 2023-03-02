# Virtual Private Networks (VPN)

## Objectives

Create VPN gateways in each network

Create VPN tunnels between the gateways

Verify VPN connectivity

## Task 1: Explore the networks and instances

Ping VM instances external IP using 
```
ping -c 3 <external IP address>
```

## Task 2: Create the VPN gateways and tunnels

Establish private communication between the two VM instances by creating VPN gateways and tunnels between the two networks.

### Reserve two static IP addresses

Reserve one static IP address for each VPN gateway.

In the Cloud Console, on the Navigation menu (Navigation menu), click VPC network > External IP addresses.

Click Reserve static address.

Specify the following, and leave the remaining settings as their defaults:
Property	Value (type value or select option as specified)
Name	vpn-1-static-ip
IP version	IPv4
Region	us-central1

Click Reserve.

Repeat the same for vpn-2-static-ip.

Click Reserve static address.

Specify the following, and leave the remaining settings as their defaults:
Property	Value (type value or select option as specified)
Name	vpn-2-static-ip
IP version	IPv4
Region	europe-west1

### Create the vpn-1 gateway and tunnel1to2

In the Cloud Console, on the Navigation menu (Navigation menu), click Hybrid Connectivity > VPN.

Click Create VPN Connection.

If asked, select Classic VPN, and then click Continue.

Specify the following in the VPN gateway section, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	vpn-1
Network	vpn-network-1
Region	us-central1
IP address	vpn-1-static-ip

Specify the following in the Tunnels section, and leave the remaining settings as their defaults:

Property	Value (type value or select option as specified)
Name	tunnel1to2
Remote peer IP address	[VPN-2-STATIC-IP]
IKE pre-shared key	gcprocks
Routing options	Route-based
Remote network IP ranges	10.1.3.0/24 [internal ip address for vm instance 2]

Equivalent command line: 
```
gcloud compute target-vpn-gateways create vpn-1 --project=qwiklabs-gcp-03-bc85da7e9da5 --region=us-central1 --network=vpn-network-1 

gcloud compute forwarding-rules create vpn-1-rule-esp --project=qwiklabs-gcp-03-bc85da7e9da5 --region=us-central1 --address=34.71.63.22 --ip-protocol=ESP --target-vpn-gateway=vpn-1 

gcloud compute forwarding-rules create vpn-1-rule-udp500 --project=qwiklabs-gcp-03-bc85da7e9da5 --region=us-central1 --address=34.71.63.22 --ip-protocol=UDP --ports=500 --target-vpn-gateway=vpn-1 

gcloud compute forwarding-rules create vpn-1-rule-udp4500 --project=qwiklabs-gcp-03-bc85da7e9da5 --region=us-central1 --address=34.71.63.22 --ip-protocol=UDP --ports=4500 --target-vpn-gateway=vpn-1 

gcloud compute vpn-tunnels create tunnel1to2 --project=qwiklabs-gcp-03-bc85da7e9da5 --region=us-central1 --peer-address=34.79.170.227 --shared-secret=gcprocks --ike-version=2 --local-traffic-selector=0.0.0.0/0 --remote-traffic-selector=0.0.0.0/0 --target-vpn-gateway=vpn-1 

gcloud compute routes create tunnel1to2-route-1 --project=qwiklabs-gcp-03-bc85da7e9da5 --network=vpn-network-1 --priority=1000 --destination-range=10.1.3.0/24 --next-hop-vpn-tunnel=tunnel1to2 --next-hop-vpn-tunnel-region=us-central1
```

Click Create.

### Create the vpn-2 gateway and tunnel2to1

Click VPN setup wizard.

If asked, select Classic VPN, and then click Continue.

Specify the following in the VPN gateway section, and leave the remaining settings as their defaults:
Property	Value (type value or select option as specified)
Name	vpn-2
Network	vpn-network-2
Region	europe-west1
IP address	vpn-2-static-ip

Specify the following in the Tunnels section, and leave the remaining settings as their defaults:
Property	Value (type value or select option as specified)
Name	tunnel2to1
Remote peer IP address	[VPN-1-STATIC-IP]
IKE pre-shared key	gcprocks
Routing options	Route-based
Remote network IP ranges	10.5.4.0/24 [internal ip address for vm instance 1]

## Task 3: Verify VPN connectivity

Now we should be able to ping both internal and external IP addresses.

### Verify server-1 to server-2 connectivity
In the Cloud Console, on the Navigation menu, click Compute Engine > VM instances.

For server-1, click SSH to launch a terminal and connect.

To test connectivity to server-2's internal IP address, run the following command:
```
ping -c 3 <insert server-2's internal IP address here>
```

### Remove the external IP addresses

Now that you verified VPN connectivity, you can remove the instances' external IP addresses. For demonstration purposes, just do this for the server-1 instance.

On the Navigation menu, click Compute Engine > VM instances.
Select the server-1 instance and click Stop. Wait for the instance to stop.
```
Instances need to be stopped before you can make changes to their network interfaces.
```

Click on the name of the server-1 instance to open the VM instance details page.
Click Edit.
For Network interfaces, click the Edit icon (Edit).
Change External IP to None.
Click Done.
Click Save and wait for the instance details to update.
Click Start.
Click Start again to confirm that you want to start the VM instance.
Return to the VM instances page and wait for the instance to start.
Notice that External IP is set to None for the server-1 instance.
