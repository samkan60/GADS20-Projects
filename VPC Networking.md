# VPC Networking

## Objectives
- Explore the default VPC network
- Create an auto mode network with firewall rules
- Convert an auto mode network to a custom mode network
- Create custom mode VPC networks with firewall rules
- Create VM instances using Compute Engine
- Explore the connectivity for VM instances across VPC 
  networks

**NB Activate Cloud Shell if you are in a browser** 

## Task 1. Explore the default network
1. View the subnets
```
gcloud compute networks list
```
2. View the routes
```
gcloud compute routes list
```
3. View the firewall rules
```
gcloud compute firewall-rules list
```
4. Delete the Firewall rules
```
gcloud compute firewall-rules delete
```
5. Delete the default network
```
gcloud compute networks delete
```
Try to create a VM instance
```
gcloud compute instances create my-vm-1 --zone=us-central1-a --machine-type=e2-medium --image=debian-9-stretch-v20200910 --subnet=default
```
This will not work since there is no VPC network.

## Task 2. Create an auto mode network
Create an auto mode VPC network with firewall rules
```
gcloud compute networks create mynetwork --subnet-mode=auto --bgp-routing-mode=regional

gcloud compute firewall-rules create mynetwork-allow-icmp    --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp

gcloud compute firewall-rules create mynetwork-allow-internal  --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all

gcloud compute firewall-rules create mynetwork-allow-rdp   --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

gcloud compute firewall-rules create mynetwork-allow-ssh 
 --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22
```
Create a VM instance in us-central1
```
gcloud compute instances create mynet-us-vm --zone=us-central1-c --machine-type=n1-standard-1 --subnet=mynetwork  --image=debian-10-buster-v20200902 
```
Create a VM instance in europe-west1
```
gcloud compute instances create mynet-eu-vm --zone=europe-west1-c --machine-type=n1-standard-1 --subnet=mynetwork --image=debian-10-buster-v20200902 
```
Verify connectivity for the VM instances

From Cloud shell 

```
ssh mynet-us-vm
```

To test connectivity to mynet-eu-vm's internal IP

```
ping -c 3 10.132.0.1
```
Convert the network to a custom mode network
```
gcloud compute networks update mynetwork --switch-to-custom-subnet-mode
```

## Task 3. Create custom mode networks

Create the managementnet network
```
gcloud compute networks create managementnet --subnet-mode=custom --bgp-routing-mode=regional

gcloud compute networks subnets create managementsubnet-us  --range=10.130.0.0/20 --network=managementnet --region=us-central1
```

Create the privatenet network

```
gcloud compute networks create privatenet --subnet-mode=custom --bgp-routing-mode=regional
```

To create the privatesubnet-us subnet, run
```
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
```

To create the privatesubnet-eu subnet, run
```
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20
```
Create firewall rules for management
```
gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0
```

Create the firewall rules for privatenet
```
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0
```

 List all the firewall rules (sorted by VPC network), run the following

```
gcloud compute firewall-rules list --sort-by=NETWORK
```

Create the managementnet-us-vm instance
```
gcloud compute instances create managementnet-us-vm --zone=us-central1-c  --subnet=managementsubnet-us --machine-type=f1-micro --subnet=managementsubnet-us --image=debian-10-buster-v20200902 
```

Create the privatenet-us-vm instance

```
gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us
```

Verify the creation of the instances
```
gcloud compute instances list --sort-by=ZONE
```

## Task 4. Explore the connectivity across networks

Ping the external IP addresses

In Cloud Shell run

```
ssh mynet-us-vm
```

To test connectivity to mynet-eu-vm's external IP
```
ping -c 3 34.76.115.41
```

To test connectivity to managementnet-us-vm's external IP
```
ping -c 3 35.239.68.123
```

Ping the internal IP addresses

SSH to mynet-us-vm
```
ssh mynet-us-vm
```

To test connectivity to mynet-eu-vm's internal IP, run 
```
ping -c 3 10.132.0.2
```

To test connectivity to managementnet-us-vm's internal IP, run
```
ping -c 3 10.130.0.2
```

To test connectivity to privatenet-us-vm's internal IP, run
```
ping -c 3 172.16.0.2
```

The End