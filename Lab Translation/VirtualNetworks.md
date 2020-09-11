# LAB : Essential Google Cloud Infrastructure: Foundation - VPC Networking

### Objectives

In this lab, you learn how to perform the following tasks:
  - Explore the default VPC network
  - Create an auto mode network with firewall rules
  - Convert an auto mode network to a custom mode network
  - Create custom mode VPC networks with firewall rules
  - Create VM instances using Compute Engine
  - Explore the connectivity for VM instances across VPC networks
 
## Task 1: Explore the default network using the GCP Console
  - View the default network
  - View the subnets
  - View the firewall rules
  
## Task 2. Create an auto mode network
  - Create an auto mode network with two VM instances 
    ```
    	gcloud compute networks create mynetwork --project=qwiklabs-gcp-04-8133a1a506b2 --description=mynetwork --subnet-mode=auto --bgp-routing-mode=regional

    ```		
  		
  - Create a VM instance in us-central1
  
  - Create a VM instance in the us-central1 region. Selecting a region and zone determines the subnet and assigns the internal IP address from the subnet's IP address range.
		
