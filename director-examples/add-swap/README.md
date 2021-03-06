# Adding Swap to Overcloud nodes during deployment

By default OSP Director will deploy nodes without any swap space.  To enable swap with a new deployment there is a documented solution as follows:

* https://access.redhat.com/solutions/2148341
* https://bugzilla.redhat.com/show_bug.cgi?id=1259539

Alternatively you can update the overcloud-full image to include a swap partition: 
``` 
NOTE: THIS IS NOT YET TESTED!
qemu-img resize overcloud-fulll.qcow2 +10G
virt-customize -a overcloud-full.qcow2 --run-command 'echo -e "n\np\n2\n\n\nt\n2\n82\nw\nq\n” | fdisk /dev/sda’  # create a partition 2 of type 82
virt-customize -a overcloud-full.qcow2 --run-command 'mkswap /dev/sda2'
virt-customize -a overcloud-full.qcow2 --run-command 'echo -e "/dev/vda2 swap swap defaults 0 0">>/etc/fstab'
```


If you've already deployed, you will not have disk space available to add more swap (unless adding a physical disk is possible).  You can allocate swap in a file manually if desired: 

WARNING: This is not something you want for production!  For that, redeploy as specified above.  But for lab this is a fine workaround
```
# As root - create 4GB swap
free
dd if=/dev/zero of=/swapfile bs=1024 count=4000000
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
free
echo "/swapfile   swap    swap    sw  0   0" >> /etc/fstab
```


