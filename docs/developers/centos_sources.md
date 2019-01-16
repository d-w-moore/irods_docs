# Centos 7 /  Redhat) plus iRODS - installation from source on a VM

## Procedure
   1. Procure a RedHat or Centos "ISO" image  (Centos-7 Minimal is small and free, and will do)
   1. use the ISO to create a VM with >=15GB disk space , 2G RAM and preferably 2 CPU's
   1. Follow the Ubuntu install instructions, altered slightly:
      - install the epel-release (also req'd for a package install)

      - `ifup eth0` (use the appropriate network interface name from `cat /proc/net/dev`)
      - `yum install -y net-tools iproute curl` (maybe  needed on Docker style or otherwise minimal images)
      - `yum install epel-release` (needed for postgresql server)
      - `yum install -y wget sudo git tig vim nano`
      - if installing from source, need `core-dev.irods.org`; else `package.irods.org`
      - from here out , can follow **INSTALLATION** instructions on irods.org, except package names
---
## Sources
   - https://www.cyberciti.biz/faq/installing-rhel-epel-repo-on-centos-redhat-7-x/
   - https://unix.stackexchange.com/questions/433046/how-do-i-enable-centos-repositories-on-rhel-red-hat
