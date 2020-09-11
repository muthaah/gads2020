# LAB : Essential Google Cloud Infrastructure: Foundation - Working with Virtual Machines

### Objectives

In this lab, you learn how to perform the following tasks:

  - Customize an application server
  - Install and configure necessary software
  - Configure network access
  - Schedule regular backups

### Hint
  - Although not documented, it is always a good idea to start all your command line work by setting your current and active cloud project i.e init your project on cloudshell
  
    ` gcloud config set project qwiklabs-gcp-04-8133a1a506b2 `

  - The Cloud Storage API seems to have changed and requires e4xtra permissions, to get this enter the command below inside the VM instance that you will created and follow the   instructions
    
    ` gcloud auth login `
    
    
 
## Task 1: Create the VM

  - Define a VM using advanced options
  
  ```
	gcloud compute --project=qwiklabs-gcp-00-3ce544c5577a instances create mc-server --zone=us-central1-a --machine-type=n1-standard-1 --subnet=default --address=34.123.156.149 --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=330908858026-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/trace.append,https://www.googleapis.com/auth/devstorage.read_write --tags=minecraft-server --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mc-server --create-disk=mode=rw,size=50,type=projects/qwiklabs-gcp-00-3ce544c5577a/zones/us-central1-a/diskTypes/pd-ssd,name=minecraft-disk,device-name=minecraft-disk --reservation-affinity=any

  ```
 

## Task 2: Prepare the data disk

  - Create a directory and format and mount the disk
    - The disk is attached to the instance, but it is not yet mounted or formatted.
    
  - From the cloud shell enter the command below to connect to the mc-server instance
    
    ` gcloud compute ssh mc-server `
    
  - Create a directory that serves as the mount point for the data disk, run the following command:

    ` sudo mkdir -p /home/minecraft `
    
  - To format the disk, run the following command:

    ` sudo mkfs.ext4 -F -E lazy_itable_init=0, lazy_journal_init=0,discard /dev/disk/by-id/google-minecraft-disk `
    
    ` sudo mount -o discard,defaults /dev/disk/by-id/google-minecraft-disk /home/minecraft `
    
  
## Task 3: Install and run the application

The Minecraft server runs on top of the Java Virtual Machine (JVM), so it requires the Java Runtime Environment (JRE) to run. Because the server doesn't need a graphical user interface, you use the headless version of the JRE. This reduces the JRE's resource usage on the machine, which helps ensure that the Minecraft server has enough room to expand its own resource usage if needed.

  - Install the Java Runtime Environment (JRE) and the Minecraft server from the SSH terminal for mc-server, run the below command

   ` gcloud compute ssh mc-server `

  ```
 	sudo apt-get update && sudo apt-get install -y default-jre-headless &&  sudo apt-get install wget
   
  ```
  
  - Navigate to the directory where the persistent disk is mounted, run the following command
  
    ` cd /home/minecraft `
    
  - Download the current Minecraft server JAR file (1.11.2 JAR), run the following command:

    ` sudo wget https://launcher.mojang.com/v1/objects/d0d0fe2b1dc6ab4c65554cb734270872b72dadd6/server.jar `
    
  - Initialize the Minecraft server, run the following command:

    ` sudo java -Xmx1024M -Xms1024M -jar server.jar nogui `
    
  - To see the files that were created in the first initialization of the Minecraft server, run the following command:

    ` sudo ls -l `
     
  - Edit the EULA, run the following command:

     ` sudo nano eula.txt `
     
    Change the last line of the file from eula=false to eula=true
    Press Ctrl+O, ENTER to save the file and then press Ctrl+X to exit nano.

  - install screen, an application that allows you to create a virtual terminal that can be "detached," becoming a background process, or "reattached," becoming a foreground    
    process. When a virtual terminal is detached to the background, it will run whether you are logged in or not. run the following command:

     ` sudo apt-get install -y screen `
    
  - Start your Minecraft server in a screen virtual terminal, run the following command: (Use the -S flag to name your terminal mcs)

     ` sudo screen -S mcs java -Xmx1024M -Xms1024M -jar server.jar nogui `
     
  - Detach the screen terminal, press Ctrl+A, Ctrl+D. The terminal continues to run in the background. To reattach the terminal, run the following command:

     ` sudo screen -r mcs `
      
  - Exit the SSH terminal, run the following command:

     ` exit `
     

## Task 4: Allow client traffic

  - Create a firewall rule
  
  ```
	gcloud compute --project=qwiklabs-gcp-00-3ce544c5577a firewall-rules create minecraft-rule --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:25565 --source-ranges=0.0.0.0/0 --target-tags=minecraft-server
  ```
  
  
## Task 5: Schedule regular backups

  - From the SSH terminal for mc-server, run the below command

   ` gcloud compute ssh mc-server `
 
   - Store the Cloud Storage Bucket name on the environment
  
    ` export YOUR_BUCKET_NAME=<Enter your bucket name here> `
  
    and Verify it with echo

    ` echo $YOUR_BUCKET_NAME `
      
  - Create a Cloud Storage bucket
    
   ` gsutil mb gs://$YOUR_BUCKET_NAME-minecraft-backup `
   
   
  - Create a backup script

    ` cd /home/minecraft `
    
    To create the script, run the following command:

    ` sudo nano /home/minecraft/backup.sh `
    
    Copy and paste the following script into the file:
    
    ```
	#!/bin/bash
	screen -r mcs -X stuff '/save-all\n/save-off\n'
	/usr/bin/gsutil cp -R ${BASH_SOURCE%/*}/world gs://${YOUR_BUCKET_NAME}-minecraft-backup/$(date "+%Y%m%d-%H%M%S")-world
	screen -r mcs -X stuff '/save-on\n'/
    ```

    Press Ctrl+O, ENTER to save the file, and press Ctrl+X to exit nano.

  - make the script executable, run the following command:

    ` sudo chmod 755 /home/minecraft/backup.sh `
    
  - Test the backup script from the SSH terminal   

    ` . /home/minecraft/backup.sh `
    
  - From the SSH terminal, open the cron table for editing:

    ` sudo crontab -e `
    
    When you are prompted to select an editor, type the number corresponding to nano, and press ENTER.    
 
  - At the bottom of the cron table, paste the following line:

    ` 0 */4 * * * /home/minecraft/backup.sh `
    
    The above line instructs cron to run backups every 4 hours.
    Press Ctrl+O, ENTER to save the cron table, and press Ctrl+X to exit nano.
   
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
