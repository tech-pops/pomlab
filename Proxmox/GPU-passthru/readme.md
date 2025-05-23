# Proxmox LXC: Building a Desktop Environment with Shared NVIDIA GPU Passthrough (Debian 12, Blender...)

## Resources we need
1. [The Original Guide by PSteinberg](https://forum.proxmox.com/threads/homeserver-how-to-make-a-lxc-based-xubuntu-20-04-desktop-enviroment-container-with-snap-and-gpu-passthru-va-api-intel-i915.76406/)

2. [C-NERGY XRDP Script Download](https://c-nergy.be/products.html)

3. [Modifying Polkit](https://mike632t.wordpress.com/2015/10/04/enable-shutdown-and-restart-for-remote-sessions/)

4. [Remmina Installation](https://remmina.org/how-to-install-remmina/)

## Installing Debian 12 LXC New method November 2024! (Install Windows Apps Video)
TODO

Installation steps Debian 12.

### Video
[Ultimate Guide to Running Windows Apps on Proxmox Debian 12 LXC: Wine, Lutris & Bottles!](https://www.youtube.com/watch?v=aZLAzZY7eWQ)

### Steps
The instructions assumes you have watched our video in installing the [Desktop Environment Video](https://www.youtube.com/watch?v=X-CxuojVS6c).
1. Create a Privileged LXC container, use the latest Debian 12 Image.
2. Add the LXC Configuration, same as below.
```
features: mount=fuse,fuse=1,nesting=1
lxc.apparmor.raw: mount,
lxc.mount.entry: /dev/fuse dev/fuse none bind,create=file 0 0
lxc.cap.drop:
lxc.cap.drop: mac_override sys_time sys_module sys_rawio
lxc.apparmor.profile: unconfined
lxc.cgroup2.devices.allow: c 226:0 rwm
lxc.cgroup2.devices.allow: c 226:128 rwm
lxc.cgroup2.devices.allow: c 4:7 rwm
lxc.cgroup2.devices.allow: c 29:0 rwm
lxc.mount.entry: /dev/dri/card0 dev/dri/card0 none bind,optional,create=file
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
lxc.mount.entry: /dev/tty7 dev/tty7 none bind,optional,create=file
lxc.mount.entry: /dev/fb0 dev/fb0 none bind,optional,create=file
lxc.cgroup.devices.allow: c 116:* rwm
lxc.mount.entry: /dev/snd dev/snd none bind,optional,create=dir
```
3. Modify the source.list file and replace it with the list below:
```
nano /etc/apt/sources.list
```

```
# Debian 12 (Bookworm) repositories
deb https://ftp.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware
deb https://ftp.debian.org/debian/ bookworm contrib main non-free non-free-firmware
deb https://ftp.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware
deb https://ftp.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware
deb https://security.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware

# Debian 12 (Bookworm) source repositories
deb-src https://ftp.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware
deb-src https://ftp.debian.org/debian/ bookworm contrib main non-free non-free-firmware
deb-src https://ftp.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware
deb-src https://ftp.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware
deb-src https://security.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware
```
4. Add i386 architecture
```
sudo dpkg --add-architecture i386
```
5. perform apt update and apt upgrade
```
apt update && apt upgrade -y
```
6. Install Tasksel and the intel-i965 driver
7. Run tasksel and choose XFCE. I also tried LXQT and it works, KDE doesn't work, will update soon which DE works.
```
tasksel
```
8. Run XRDP script with -s, additionally add -u if you choosed XFCE.
9. The rest of the instruction is the same as the video now. Which is after the installation of the XRDP Script.
10. I disabled power management and light locker in the Power Manager in the Desktop Connection.


# Additional Steps from the video (LXC Desktop Environment Video)
The following are additional steps from the video, there are specific commands from a specific timestamp in the video:

### Checking which card number your GPU uses
```
ls /dev/dri
```

### Complete Lines from the LXC Configuration, Including the extra 2 lines at @2:56 from the video
This is the complete text you need to copy to the configuration, already modified to cgroup2, but please check your card number first. 
```
features: mount=fuse,fuse=1,nesting=1
lxc.apparmor.raw: mount,
lxc.mount.entry: /dev/fuse dev/fuse none bind,create=file 0 0
lxc.cap.drop:
lxc.cap.drop: mac_override sys_time sys_module sys_rawio
lxc.apparmor.profile: unconfined
lxc.cgroup2.devices.allow: c 226:0 rwm
lxc.cgroup2.devices.allow: c 226:128 rwm
lxc.cgroup2.devices.allow: c 4:7 rwm
lxc.cgroup2.devices.allow: c 29:0 rwm
lxc.mount.entry: /dev/dri/card0 dev/dri/card0 none bind,optional,create=file
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
lxc.mount.entry: /dev/tty7 dev/tty7 none bind,optional,create=file
lxc.mount.entry: /dev/fb0 dev/fb0 none bind,optional,create=file
lxc.cgroup.devices.allow: c 116:* rwm
lxc.mount.entry: /dev/snd dev/snd none bind,optional,create=dir
```

### Adding the root user to groups (Replace morpheus to your user)
```
sudo usermod -aG sudo morpheus
sudo usermod -aG video morpheus
sudo usermod -aG render morpheus
sudo usermod -aG audio morpheus
sudo usermod -aG input morpheus
sudo usermod -aG syslog morpheus
```

### Additional Commands Before Running the XRDP Script @6:34 from the video (If you want Audio, Also replace morpheus to your user)
```
sudo mkdir -p /run/user/1000
sudo chown morpheus:morpheus /run/user/1000
export XDG_RUNTIME_DIR=/run/user/1000
source ~/.bashrc  # or ~/.zshrc
```

### Whether to have audio or not
You can run the script with or without audio, the following 2 are the commands for:

#### With audio (change 1.5.2 if you've downloaded a newer version)
```
./xrdp-installer-1.5.2.sh -s
```

#### Without audio (change 1.5.2 if you've downloaded a newer version)
```
./xrdp-installer-1.5.2.sh
```

### Getting the LAN IP
```
ip a | grep 192.168
```

## Command Corrections
Please take note of the following corrected commands to input in the terminal:

### Running Tasksel
```
tasksel install xubuntu-desktop
```

### Installing XCFE components
```
sudo apt install xfce4 xfce4-goodies
```

## Can i switch DE?
Sure, as long as you're using ubuntu 20, it would be fine. But using something else like ubuntu 22 to 24 or debian 11 to 12, you might have errors and would require to troubleshoot. It's not impossible but it would take time to trouble, the guide i showed was the probably the fastest way to get it up and running while having to minimize troubleshooting issues.

## I tried running vulkaninfo/vainfo and ended up with an error
The guide was focused on the older generation intel gpu's, the fundamental problem to this error is most likely is that the graphics driver is not fully passthroughed to the system. Please make sure it's a priveleged container, if the graphics driver is present in the LXC itself but you still cannot run programs, its more of a software problem, it's best to try solving it with chatGPT first. 

## I tried running an application but it doesn't seem to work with the user i created
It's sometimes like that, for example you tried to install a flatpak program but running it gives you a bwrap error, the solution that i found was you need to run it as a root user. But it has alot of steps involved. 

### 1. Check your display number in your user (ex. morpheus) and not root
example it gave you an output of DISPLAY 13.0
```
echo $DISPLAY
```

### 2. Run these commands in your user (ex. morpheus) and not root (change morpheus to your user)
```
export XAUTHORITY="$XDG_RUNTIME_DIR"/Xauthority
xhost +SI:localuser:root
xhost +SI:localuser:morpheus
```

### 3. Export your display in your root (not morpheus)
example we said it was 13.0
```
export DISPLAY=:13.0
```

### 4. Run the program
if you run into problems with directory it's probably the program is not using the correct directory for root. Use chatgpt to help you give the correct command to overwrite the directory, heres an example the program bottles (used to run windows applications and games). We set the filesystem to /root/ so that it can properly see the files with the root user.
```
flatpak override --user com.usebottles.bottles –filesystem=/root/
```
Explain
