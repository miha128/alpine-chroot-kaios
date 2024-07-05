# alpine chroot KaiOS
chroot into alpine linux using a nokia 6300 4g as an example.
prequisites:
**ROOTED** phone.
adb shell access with root privileges.
*(optional, mocha vnc server if you want to use a pretty primitive GUI.)*

to start, get shell access.
`adb root && adb shell`

your shell should look like this
`root@Nokia 6300 4G:/ #`

 cd into data and make a new folder titled ubuntu, or any name you want, make sure to edit the script after.
`cd data && mkdir ubuntu && cd ubuntu`

download the alpine rootfs with cURL from https://alpinelinux.org/downloads/ 
if the command below fails, replace with http, beware if you're using public wifi tho.

`curl -o rootfs https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/armhf/alpine-minirootfs-3.20.1-armhf.tar.gz`

http version

`curl -o rootfs http://dl-cdn.alpinelinux.org/alpine/v3.20/releases/armhf/alpine-minirootfs-3.20.1-armhf.tar.gz`

to extract it, we have to use the built-in busybox binary for our system, the embedded tar binary throws an error.

`busybox tar -xvzf rootfs`

our folder structure should look something like this, verify with ls -l 

`drwxr-xr-x root     root              2024-07-05 10:32 bin`  
`drwxrwxrwx root     root              2024-07-05 11:54 data`  
`drwxr-xr-x root     root              2024-06-18 17:17 dev`  
`drwxr-xr-x root     root              2024-07-05 11:26 etc`  
`drwxr-xr-x root     root              2024-06-18 17:17 home`  
`drwxr-xr-x root     root              2024-07-05 11:24 lib`  
`drwxr-xr-x root     root              2024-07-04 23:23 media`  
`drwxr-xr-x root     root              2024-06-18 17:17 mnt`  
`drwxr-xr-x root     root              2024-06-18 17:17 opt`  
`dr-xr-xr-x root     root              2024-06-18 17:17 proc`  
`drwx------ root     root              2024-07-05 11:23 root`  
`drwxr-xr-x root     root              2024-06-18 17:17 run`  
`drwxr-xr-x root     root              2024-07-05 10:35 sbin`  
`drwxr-xr-x root     root              2024-06-18 17:17 srv`  
`drwxr-xr-x root     root              2024-06-18 17:17 sys`  
`drwxrwxrwt root     root              2024-07-05 11:58 tmp`  
`drwxr-xr-x root     root              2024-07-05 10:32 usr`  
`drwxr-xr-x root     root              2024-07-04 23:23 var`  

*note: i deleted the rootfs file, i don't need it, you can delete it too with `rm rootfs`.*

now, it's time to "bind" our filesystem, so the rootfs can actually be usable
save this as a script, or copy and paste it into adb shell

`busybox mount -o bind /sys /data/ubuntu/sys`  

`busybox mount -o bind /proc /data/ubuntu/proc`  

`busybox mount -o bind /dev/pts /data/ubuntu/dev/pts`  

`busybox mount -o bind /dev/ashmem /data/ubuntu/dev/ashmem`  

`busybox mount -o rbind /dev /data/ubuntu/dev`  

`export PATH=/bin:/sbin:/usr/bin:/usr/sbin`  

`export TERM=$TERM`  

`export TMPDIR=/tmp`  

`/system/bin/chroot .`

*note: /dev/pts is it's own fs, we have to mount it separately.*

once this is done, you're going to be presented with something like `/ #`
now there's just a little more configuration before our machine can access websites. *heavy emphasis on websites, we don't have a nameserver.*

run this command to use google's DNS as a nameserver, so we can update our packages.
`echo "nameserver 8.8.8.8" > /etc/resolv.conf

we can now install any packages we want (as long as they're in alpine linux's repos.)
run `apk update` to update your packages.

installing a package is done with `apk add *package name*`


**good job! you made it to the end! enjoy your chroot!**


unless, you want to also have a GUI accessible with a vnc server, we MUST do something about this.

run these in order.

`apk add alpine-conf`
`setup-xorg-base`
`apk add x11vnc xvfb`

run with this specific command.

`x11vnc -create -noshm -forever`

without -noshm you will NOT be able to use a GUI because of a bug.

if i omitted something, send it in the issues tab, i'll look at it.
