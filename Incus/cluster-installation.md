# Incus cluster installation on Ubuntu 24.04 LTS

For this setup, I chose to use stable packages from Zabbly https://github.com/zabbly/incus

sources:


## final architecture scheme

## installation of the 1st node

1. Repository key

  - check the fingerprint of the key:

        curl -fsSL https://pkgs.zabbly.com/key.asc | gpg --show-keys --fingerprint
  - you shou get:

        pub   rsa3072 2023-08-23 [SC] [expires: 2025-08-22]
        4EFC 5906 96CB 15B8 7C73  A3AD 82CC 8797 C838 DCFD
        uid                      Zabbly Kernel Builds <info@zabbly.com>
        sub   rsa3072 2023-08-23 [E] [expires: 2025-08-22]
  - check if directory for keyrings exists

        mkdir -p /etc/apt/keyrings/
  - Save the key locally:

        curl -fsSL https://pkgs.zabbly.com/key.asc -o /etc/apt/keyrings/zabbly.asc
  - 
