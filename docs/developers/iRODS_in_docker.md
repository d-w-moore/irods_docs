# iRODS Development
   - running from package installation
      - [packages](http://packages.irods.org) site
   - full featured builds for test and debugging
      - [core-dev](http://core-dev.irods.org) site
   - Choice of platforms for running, building and debugging
      - Linux: Ubuntu 16.04
      - Virtual machines
          - on **Ubuntu** : with virt-manager (QEMU/KVM)
             - for Ubuntu 18, use Server Install Image (not the live server image) from [here](http://cdimage.ubuntu.com/ubuntu/releases/bionic/release/)
          - on **MacOS**  : Parallels
          - on **Windows** : Hyper V
      - Docker
   - shaping data policy in the iRODS Rule Language
   - writing new microservices
   - creating iRODS clients with the Python irods-client

## Tools of the trade

We'll begin by installing two tools which (outside of compiler, code analysis and debugging tools) will be of both immediate and continued value to us:

   - `git` - source code manager
   - `docker` - container environment

## Setting up for using docker

Docker containers can provides a quick, demonstration-ready and reproduceable environment in which to experiment/develop with and on iRODS.

For MacOS or Windows 10, go to the [docker website](http://docker.com) and download / install the Docker Desktop. (Note this is not possible on lower versions of Windows; and, on MacOS 10 <= x < 12.10, use Docker Toolbox instead.)

   - on Ubuntu 16, a satisfactory version of docker is very readily installed via APT:
      - `apt install docker.io`
      - (as root/sudo) `gpasswd -a myuser docker` (and log out/back in).

## A running start ...

Assuming docker and git are installed, it is a very quick path to getting up-and-running on a basic, one-node iRODS installation.

We begin by cloning a repository from which an iRODS system can be installed and stably and restartably run in a Ubuntu 16 environment.

```
ub16:~$ git clone http://github.com/d-w-moore/irods_docker/
ub16:~$ cd irods_docker ; git checkout dan_restartable_4.2
ub16:~$ cd restartable_4.2
ub16:~$ docker build -t irods42_pkg_install .
```

Within  a few minutes, the build is complete and we can start an application container in which an iRODS server will be automatically installed and executed:


```
ub16:~$ docker run -dit irods42_pkg_install
b0f3223e1e76e5fbfe48d2f20fb9f49d1f92ef786facc775d31c9d524228d87f
17:50PM (dan_restartable_4.2) daniel@daniel-ThinkPad-W540:~/irods_docker/restartable_4.2 $ docker ps
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS               NAMES
b0f3223e1e76        irods42_pkg_install   "/run_irods.sh"     2 minutes ago       Up 2 minutes                            festive_bohr
```

The container can be referred to using a unique initial subset of its SHA, or the instance name (here, `festive_bohr`):
```
ub16:~$ docker logs b0

docker exec festive_bohr ps -ef
    ...
    ...
Putting the test file into iRODS...
Getting the test file from iRODS...
Removing the test file from iRODS...
Success.

+--------------------------------+
| iRODS is installed and running |
+--------------------------------+

 --> IRODS Server is running

```

Once the logs show evidence that iRODS is ready, we can use `docker exec` either to show the processes now running:
```
ub16:~$ docker exec festive_bohr ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 22:50 pts/0    00:00:00 /bin/bash /run_irods.sh
postgres    30     1  0 22:50 ?        00:00:00 /usr/lib/postgresql/9.5/bin/postgres -D /var/lib/postgresql/9.5/main -c config_file=/etc/postgresql/9.5/main/postgresql.conf
postgres    32    30  0 22:50 ?        00:00:00 postgres: checkpointer process   
postgres    33    30  0 22:50 ?        00:00:00 postgres: writer process   
postgres    34    30  0 22:50 ?        00:00:00 postgres: wal writer process   
postgres    35    30  0 22:50 ?        00:00:00 postgres: autovacuum launcher process   
postgres    36    30  0 22:50 ?        00:00:00 postgres: stats collector process   
irods       79     1  0 22:50 ?        00:00:00 /usr/sbin/irodsServer
irods       80    79  0 22:50 ?        00:00:00 /usr/sbin/irodsServer
irods       82    79  0 22:50 ?        00:00:00 irodsReServer
root       104     1  0 22:50 pts/0    00:00:00 sleep 2147483647d
root       163     0  0 23:00 ?        00:00:00 ps -ef

```
or we can create a virtual terminal and enter the container to interact with iRODS:
```
ub16:~$ docker exec -it festive_bohr /bin/bash
root@b0f3223e1e76:/# su - irods
irods@b0f3223e1e76:~$ ls
VERSION.json  VERSION.json.dist  Vault	clients  config  configuration_schemas	irodsctl  log  msiExecCmd_bin  packaging  scripts  test
irods@b0f3223e1e76:~$ ils     
/tempZone/home/rods:
irods@b0f3223e1e76:~$ iput VERSION.json
irods@b0f3223e1e76:~$ ils
/tempZone/home/rods:
  VERSION.json
irods@b0f3223e1e76:~$ imeta set -d VERSION.json my_key some_value
irods@b0f3223e1e76:~$ imeta ls -d VERSION.json
AVUs defined for dataObj VERSION.json:
attribute: my_key
value: some_value
units:
irods@b0f3223e1e76:~$
```
