# iRODS on CentOS 7 - Single node, ICAT on PostgreSQL

## Preliminary:

   - Centos needs [EPEL](https://fedoraproject.org/wiki/EPEL) in order to install PostgreSQL

   - `yum install -y epel-release wget sudo`

   - `yum install postgresql-server`
   - `su - postgres -c "pg_ctl initdb"`

   - substitutions to be made in `~postgres/data/pg_hba.conf` :
   ```
   # sed -i".orig" 's/^\s*\(host\s\s*all\s\s*all.*\s\s*\)\(trust\)\s*$/\1md5/' ~postgres/data/pg_hba.conf
   # diff ~postgres/data/pg_hba.conf   #--> should show two lines affected
   ```
   - change to `postgres` user , start the db server and create `ICAT`
   - in a full VM, one can use service or systemctl to start/enable the database
   ```
   # su - postgres
   # pg_ctl start
   ```


   As always,
      - APT/YUM directions for packages installation: [link](http://packages.irods.org)

   - https://docs.irods.org/4.2.5/getting_started/installation/ - irods
   - selinux disable , firewalld - disable

`docker pull mjstealey/irods-icommands:4.2.2`
```
docker run -it -e IRODS_HOST=172.17.0.2  mjstealey/irods-icommands:4.2.2 /bin/bash
irods@98f323148c6e:/local$ iput ~/clients/icommands/test/rules/testsuite1.r
irods@98f323148c6e:/local$ ils
/tempZone/home/rods:
  testsuite1.r

```
