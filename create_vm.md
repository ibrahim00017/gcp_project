# Creating Virtual Machines


To see information about unused and used memory and swap space on your custom VM,
```
free
```
To see details about the RAM installed on your VM
```
sudo dmidecode -t 17
```
To verify the number of processors
```
nproc
```
To see details about the CPUs installed on your VM
```
lscpu
```
To format the disk
```
sudo mkfs.ext4 -F -E lazy_itable_init=0,\
lazy_journal_init=0,discard \
/dev/disk/by-id/google-minecraft-disk
```
To mount the disk, run the following command:

```
sudo mount -o discard,defaults /dev/disk/by-id/google-minecraft-disk /home/minecraft
```

