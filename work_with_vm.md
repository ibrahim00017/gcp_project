# Working with Virtual Machines

## Prepare the data disk
### Create a directory and format and mount the disk
To create a directory that serves as the mount point for the data disk, run the following command:
```
sudo mkdir -p /home/minecraft
```
To format the disk, run the following command:
```
sudo mkfs.ext4 -F -E lazy_itable_init=0, lazy_journal_init=0,discard /dev/disk/by-id/google-minecraft-disk
```
To mount the disk, run the following command:

```
sudo mount -o discard,defaults /dev/disk/by-id/google-minecraft-disk /home/minecraft
```

## Install and run the application
Install the Java Runtime Environment (JRE) 
```
sudo apt-get update
sudo apt-get install -y default-jre-headless
```

To download the current Minecraft server JAR file (1.11.2 JAR)

```
cd /home/minecraft
sudo apt-get install wget
sudo wget https://launcher.mojang.com/v1/objects/d0d0fe2b1dc6ab4c65554cb734270872b72dadd6/server.jar
```

### Initialize the Minecraft server
To initialize the Minecraft server, run the following command:
```
sudo java -Xmx1024M -Xms1024M -jar server.jar nogui
```

### Create a virtual terminal screen to start the Minecraft server

To install screen, run the following command:
```
sudo apt-get install -y screen
```
To start your Minecraft server in a screen virtual terminal, run the following command: (Use the -S flag to name your terminal mcs)

```
sudo screen -S mcs java -Xmx1024M -Xms1024M -jar server.jar nogui
```
### Detach from the screen and close your SSH session
To detach the screen terminal, press Ctrl+A, Ctrl+D. The terminal continues to run in the background. To reattach the terminal, run the following command:

```
sudo screen -r mcs
```
To exit the SSH terminal, run the following command:

```
exit
```

## Schedule regular backups
### Create a Cloud Storage bucket
```
export YOUR_BUCKET_NAME=<Enter your bucket name here>
echo $YOUR_BUCKET_NAME
gsutil mb gs://$YOUR_BUCKET_NAME-minecraft-backup

```

### Create a backup script
In the mc-server SSH terminal, navigate to your home directory:
```
cd /home/minecraft
```
#### Test the backup script and schedule a cron job

To create the script, run the following command:
```
sudo nano /home/minecraft/backup.sh
```
Copy and paste the following script into the file:

```. /home/minecraft/backup.sh```

``` sudo crontab -e```

At the bottom of the cron table, paste the following line:

```
0 */4 * * * /home/minecraft/backup.sh
```
##  Server maintenance

### Connect via SSH to the server, stop it and shut down the VM

In the mc-server SSH terminal, run the following command:

```
sudo screen -r -X stuff '/stop\n'
```


