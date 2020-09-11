# LAB : Essential Google Cloud Infrastructure: Foundation - Creating Virtual Machines

### Objectives
In this lab, you explore the available options for VMs and see the differences between locations and learn how to perform the following tasks:

  - Create several standard VMs
  - Create advanced VMs

### Hint
    Although not documented, it is always a good idea to start all your command line work by setting your current and active cloud project i.e init your project on cloudshell
  
    ` gcloud config set project qwiklabs-gcp-00-12ae7b9a6074 `


## Task 1: Create a utility virtual machine

  - Create a Virtual Machine
  
  ```
	gcloud compute --project=qwiklabs-gcp-00-12ae7b9a6074 instances create demo-vm --zone=us-central1-c --machine-type=n1-standard-1 --subnet=default --no-address --maintenance-policy=MIGRATE --service-account=355647754959-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=demo-vm --reservation-affinity=any
  ```

## Task 2: Create a Windows virtual machine

  - Create a windows VM Instance and Allow HTTP and HTTPS traffic 
 
 ```
	gcloud compute --project=qwiklabs-gcp-00-12ae7b9a6074 instances create demo-win-vm --zone=europe-west2-a --machine-type=n1-standard-2 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=355647754959-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd --boot-disk-device-name=demo-win-vm --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any
```

  - Allow HTTP traffic
  
  ```
 	gcloud compute --project=qwiklabs-gcp-00-12ae7b9a6074 firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
 ```
 
  - Allow HTTPS traffic
  
  ```
  	gcloud compute --project=qwiklabs-gcp-00-12ae7b9a6074 firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server
  ```

## Task 3: Create a custom virtual machine

  - Create a Virtual Machine Instance

  ```
  gcloud compute --project=qwiklabs-gcp-00-12ae7b9a6074 instances create mycustom-vm --zone=us-west1-b --machine-type=custom-6-32768 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=355647754959-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mycustom-vm --reservation-affinity=any
  ```

  - Connect via SSH to your custom VM
  
   	` gcloud compute ssh mynet-us-vm `

  - To see information about unused and used memory and swap space on your custom VM, run the following command:

	` free `
	
  - To see details about the RAM installed on your VM, run the following command:

	` sudo dmidecode -t 17 `
	
  - To verify the number of processors, run the following command:

	` nproc `
	
  - To see details about the CPUs installed on your VM, run the following command:

	` lscpu `
	
  - To exit the SSH terminal, run the following command:

	` exit `
	

### END OF TRANSLATION



