# Implement Private Google Access and Cloud NAT

## Create a VPC network

```
gcloud compute networks create 	privatenet --subnet-mode=custom
```
### Create subnet in region us-central1
```
gcloud compute networks subnets create privatenet-us --network=privatenet --region=us-central1 --range=10.130.0.0/20
```

## Create firewall rules
```
gcloud compute firewall-rules create privatenet-allow-ssh --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=tcp:22 --source-ranges=35.235.240.0/20
```

### Create the VM instance with no public IP address
```
gcloud compute instances create vm-internal --zone=us-central1-c --machine-type=f1-micro --no-address  --subnet=privatenet-us --image-family=debian-10 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=vm-internal
```
SSH to vm-internal to test the IAP tunnel

```
gcloud compute ssh vm-internal --zone us-central1-c --tunnel-through-iap
```


Copy an image file into your bucket

```
gsutil cp gs://cloud-training/gcpnet/private/access.svg gs://[my_bucket]
```


