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
  
## Task 2. Create an auto mode network with firewall rules
  - Create an auto mode network with two VM instances.
    
### Hint
    Although not documented, it is always a good idea to start all your command line work by setting your current and active cloud project i.e init your project on cloudshell
  
    ` gcloud config set project qwiklabs-gcp-04-8133a1a506b2 `
  
  - Enter the below commands one after the other to create the Network and firewall rules 
  
    ```
    	gcloud compute networks create mynetwork --project=qwiklabs-gcp-04-8133a1a506b2 --description=mynetwork --subnet-mode=auto --bgp-routing-mode=regional

	gcloud compute firewall-rules create mynetwork-allow-internal --project=qwiklabs-gcp-04-8133a1a506b2 --network=projects/qwiklabs-gcp-04-8133a1a506b2/global/networks/mynetwork --description=Allows\ connections\ from\ any\ source\ in\ the\ network\ IP\ range\ to\ any\ instance\ on\ the\ network\ using\ all\ protocols. --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all

     	gcloud compute firewall-rules create mynetwork-allow-ssh --project=qwiklabs-gcp-04-8133a1a506b2 --network=projects/qwiklabs-gcp-04-8133a1a506b2/global/networks/mynetwork --description=Allows\ TCP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 22. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22

     	gcloud compute firewall-rules create mynetwork-allow-rdp --project=qwiklabs-gcp-04-8133a1a506b2 --network=projects/qwiklabs-gcp-04-8133a1a506b2/global/networks/mynetwork --description=Allows\ RDP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 3389. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

    ```		
  		
  - Create a VM instance in us-central1 region.
    ```
	gcloud compute --project=qwiklabs-gcp-04-8133a1a506b2 instances create mynet-us-vm --zone=us-central1-c --machine-type=n1-standard-1 --subnet=mynetwork --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=948991764381-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mynet-us-vm --reservation-affinity=any
   
    ```
   
  - Create a VM instance in the europe-west1 region.
    ```
	gcloud compute --project=qwiklabs-gcp-04-8133a1a506b2 instances create mynet-eu-vm --zone=europe-west1-c --machine-type=n1-standard-1 --subnet=mynetwork --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=948991764381-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mynet-eu-vm --reservation-affinity=any

    ```

  - Verify connectivity for the VM instances 
    -  test connectivity to mynet-eu-vm's internal IP from mynet-us-vm instance
    
    from the cloud shell enter the command below to connect to the mynet-us-vm instance
    
    ` gcloud compute ssh mynet-us-vm `
    
    from inside the mynet-us-vm instance enter the below command to ping mynet-eu-vm instance
    
    ` ping -c 3 mynet-eu-vm  `
    
    test connectivity to mynet-eu-vm's external IP, run the following command, replacing mynet-eu-vm's external IP

    ` ping -c 3 <Enter mynet-eu-vm's external IP here> `

 
## Task 3. Create custom mode networks    
  - create two additional custom networks, managementnet and privatenet, along with firewall rules to allow SSH, ICMP, and RDP ingress traffic and VM instances
    
    - Create the managementnet network and Subnet
    
    ` gcloud compute networks create managementnet --project=qwiklabs-gcp-04-8133a1a506b2 --subnet-mode=custom --bgp-routing-mode=regional `

    ` gcloud compute networks subnets create managementsubnet-us --project=qwiklabs-gcp-04-8133a1a506b2 --range=10.130.0.0/20 --network=managementnet --region=us-central1 `
      
    - Create the privatenet network    
    
    ` gcloud compute networks create privatenet --subnet-mode=custom `   
    	
    - Create the privatesubnet-us subnet, run the following command:

    	` gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24 `

    - Create the privatesubnet-eu subnet, run the following command:

    	` gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20 `

  - list the available VPC networks, run the following command

	` gcloud compute networks list `
	
		
