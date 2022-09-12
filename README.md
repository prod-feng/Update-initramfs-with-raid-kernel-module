# Update-initramfs-with-raid-kernel-module

## 1. Oracle Linux 8.6
I installed an Oracle Linux 8.6 on a spare computer, and then wanted to move it on an old Dell server(PowerEdge R420), which has a Perc H310 raid card. 
This did not work, since the default initramfs can not recognize the H310 card(SAS 2008).

The good thing is that the Oracle Linux 8.6 has the megaraid_sas module included in the kernel already, so the onlything I need to 
do is to add this megaraid_sas module into the initramfs image, so it can recognize the H310 card at boot and mount the hard drive.

So, I boot the hard drive on the spare computer server, and I used the command below to updated the initramfs image:

```text
mkinitrd  -f initramfs-5.4.17-2136.310.7.1.el8uek.x86_64.img5 5.4.17-2136.310.7.1.el8uek.x86_64 --preload 'megaraid_sas' --preload 'mpt3sas'
```

Once it's done, move the hard drive into the Dell server and it boots smoothly.

(Oracle Linux 8.6's kernel modules support more older hardware)


## 2. Rocky Linux 8.6

I have another server has an even older raid card of (SAS 2004). My Rock Linux server does not recognize the hard drives connected to the 
card(which is in IT mode).

The big issue is that the mpt3sas module comes with Rocky linux 8.6 does bot support SAS-2 anymore. The only work around for this issue is to install a 
different module file supporting SAS-2 from ELRPO repository:

```text
# first unload the default mpt3sas module fron kernel
rmmod mpt3sas
#
yum install elrepo-release

#then

yum install kmod-mpt3sas

# load the new mpt3sas kernel
modprob mpt3sas
```

Done.
