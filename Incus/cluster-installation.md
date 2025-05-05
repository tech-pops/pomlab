# Incus cluster installation on Ubuntu 24.04 LTS

For this setup, I chose to use stable packages from Zabbly https://github.com/zabbly/incus

sources:


## final architecture scheme

## installation of the 1st node

### 1. prerequisites

  - check the fingerprint of the key:

        curl -fsSL https://pkgs.zabbly.com/key.asc | gpg --show-keys --fingerprint
  - you shou get:

        pub   rsa3072 2023-08-23 [SC] [expires: 2025-08-22]
        4EFC 5906 96CB 15B8 7C73  A3AD 82CC 8797 C838 DCFD
        uid                      Zabbly Kernel Builds <info@zabbly.com>
        sub   rsa3072 2023-08-23 [E] [expires: 2025-08-22]
  - check if directory for keyrings exists:

        mkdir -p /etc/apt/keyrings/
  - if not, create it:

        sudo mkdir /etc/apt/keyrings/
  - Save the key locally:

        sudo curl -fsSL https://pkgs.zabbly.com/key.asc -o /etc/apt/keyrings/zabbly.asc
  - Add this apt source as root (sudo -i):

        sh -c 'cat <<EOF > /etc/apt/sources.list.d/zabbly-incus-stable.sources
        Enabled: yes
        Types: deb
        URIs: https://pkgs.zabbly.com/incus/stable
        Suites: $(. /etc/os-release && echo ${VERSION_CODENAME})
        Components: main
        Architectures: $(dpkg --print-architecture)
        Signed-By: /etc/apt/keyrings/zabbly.asc

        EOF'
  - Update your system:

        apt-get update && apt-get upgrade -y
### 2. Create a Btrfs partition
  #### Create a partition on your disk

  in my case, I have 2 disks. One with 128G of system partition and some free space. I used fdisk to create a new partition in the free space, and Incus commad to create another  storage pool on the second.
  - Use fdisk with caution, 
### 3. Incus installation

  - Install incus, and Incus web UI:

        apt-get install incus incus-ui-canonical -y
  - 
