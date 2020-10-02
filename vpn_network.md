# VPC Networking
## Create the privatenet network
crate network name  privatenet type ```custom```
```
gcloud compute networks create privatenet --subnet-mode=custom
```

crate private subnet name privatesubnet-us in privatenet in us region
```
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24

```

crate private subnet name privatesubnet-eu in privatenet in europe region

```
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/24

```

list available VPC networks
```
gcloud compute networks list
```

## Create the firewall rules for privatenet
create the privatenet-allow-icmp-ssh-rdp

```
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0
```
To list all the firewall rules (sorted by VPC network)

```
gcloud compute firewall-rules list --sort-by=NETWORK
```

## Create the privatenet-us-vm instance

Create the privatenet-us-vm instance

```
gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatenet-us --image-family=debian-10 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=privatenet-us-vm
```


