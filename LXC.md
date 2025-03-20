# Find Device Numbers
```
ls -l /dev/dri
```
in my case,
renderD128 means my RTX 2060

# Find Group Numbers
```
cat /etc/group
```
in my case,
video is GUID 44 and render is GUID 105

# Add Group Numbers Values to subgid
Change values to map the two above
```
nano /etc/subgid
```
Paste at the bottom, for example:
```
root:44:1
root:105:1
```

# Create CT Using Wizard. 
Run the container and find GUIDs
## Find Group Numbers
```
cat /etc/group
```
in my case,
video is GUID 44 and render is GUID 104

## Then edit .conf In /etc/pve/lxc
Edit your device IDs and renderD***. Ensure you match the idmap values. 

```
arch: amd64
cores: 2
cpulimit: 2
features: nesting=1
hostname: test-gpu-04
memory: 3000
net0: name=eth0,bridge=vmbr0,firewall=1,hwaddr=BC:24:11:06:18:78,ip=dhcp,type=veth
ostype: debian
rootfs: local-lvm:vm-104-disk-0,size=20G
swap: 512
unprivileged: 1
```
Then add video and renderer devices:
```
lxc.cgroup2.devices.allow: c 226:0 rwm
lxc.cgroup2.devices.allow: c 226:128 rwm
```
Mount the renderer device:
```
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
```
Map UIDs from LXC UIDs to host UIDs
```
lxc.idmap: u 0 100000 65536
```
In order to only map video (44) and renderer (104) from LXC GIDs to the host vdeo & renderer groups, and let others GIDs unprivileged, map GUIDs as follow:
- map LXC GIDs 0-43 to host GIDs 100000-(+44 because starting from 0)
- map LXC GID 44 (video) to host GID 44 (video)
- map LXC GIDs 45-03 to host GIDs 100045-(+(104-45=59)
- map LXC GID 104 (renderer) to host GID 105 (renderer)
- map the rest of LXC GIDs 105-65536 to host GIDs 100105-(+(65536-105=65431))
```
lxc.idmap: g 0 100000 44
lxc.idmap: g 44 44 1
lxc.idmap: g 45 100045 59
lxc.idmap: g 104 105 1
lxc.idmap: g 105 100105 65431
```

# Add Root to Groups
Do this on your Proxmox Host
```
usermod -aG render,video root
```
To be continued
