# dockerRAM
Installs the docker snap in a tmpfs RAM-Disk and provides utility for that

You might ask yourself why you would want docker to be installed completely in a RAM-Disk.

1. It's fast
2. You can easily manage different states of the whole docker installation by putting the $HDD directory under version control
3. You can umount the RAM-disks to discard stuff.

The executer of these scripts is completly responsible for all damages this script might do to the system.
Pull requests are very welcome.

# Installation

This is only testet on Ubuntu 18.04. ```snap``` should be installed and your system should use ```systemd``` for this to work.
Both of these requirements are filled by using Ubuntu 18.04.

You should already have a line like this:
```
tmpfs /tmp tmpfs defaults,noatime,mode=1777 0 0
```
in your /etc/fstab. You might want to read https://askubuntu.com/questions/173094/how-can-i-use-ram-storage-for-the-tmp-directory-and-how-to-set-a-maximum-amount for limiting the size. Remember that other software might have significant amounts of data in your /tmp folder. Such software would crash if no space was left on your RAM.

```Reboot``` or ```mount -a #not recommended``` to complete your first step into tmpfs.

Edit dockerNEW, dockerRAM, dockerSAV, dockerKIL, dockerINF and change the location of your $TMPFS and your $HDD according to your system.
Then create these folders:
```
sudo bash
mkdir -p /$HDD/docker
mkdir -p /$HDD/dockerprg
chmod 700 /$HDD/docker
chmod 700 /$HDD/dockerprg
```
I personally don't like to create a docker group on my linux systems. Thats why i put the scripts of this repository into
```/root/bin/```. You might choose differently depending on your situation.
```
mkdir -p /root/bin
cd /root/bin
git clone https://github.com/Lamaun/dockerRAM.git
mv dockerRAM/docker* .
chmod 700 * # might change other scripts in this folder
rm -rf dockerRAM/
```
Installation complete.

# Usage
- dockerNEW
  - removes the snap docker
  - installs a new snap docker in RAM
  - disables and stops the services of docker
- dockerSAV
  - writes to disk
- dockerRAM
  - loads from disk
  - starts docker
- dockerINF
  - prints some info about the current state
- dockerKIL
  - stops docker
  - frees ram
  
Installation of a new docker should be done in the following way:
```
dockerNEW # Install to RAM
dockerSAV # Copy to HDD
dockerRAM # Load from HDD and start dockerd
```
After some dockery you use dockerSAV to save your progress to disk.
If your disk is a git repo you can than commit your progress.

After a reboot you only need ```dockerRAM``` or if you want to go back to a different version of your docker installation
use the git first.

# Troubleshoot
Issues I had at some point:
- ubuntu image got updated, somehow broke apt in the containers i had saved. -> rebuild after dockerNEW
- snap installed a new version of its core (Two files match core_* in /var/lib/snapd/snaps/) -> rm the one with the smaller number
