## Bare Metal build

   - Preliminary. Use YUM or APT depending on OS, to download dependencies.
    (a good source for these is:
      https://github.com/korydraughn/irods_docker/tree/master/just_stand_it_up
    or, for CentOS:
    https://github.com/korydraughn/irods_docker/tree/centos/just_stand_it_up
    Especially essential are the `irods-externals` packages. ninja-build is convenient because it senses and uses all available CPUs by default.
   - Once the http://core-dev.irods.org repo is enabled, these can be installed with:
    `yum install irods-externals.\*`
   - Ensure the latest cmake is in PATH : eg `/opt/irods-externals/cmake3.11.4-0/bin/cmake`
   - install the `ninja-build` package (`sudo` also for best results, and lobby your admin to
     be included in group `wheel` or `sudo`  or similar, for package install privileges via `sudo`)
   - Decide on the **branch** to build. `master` is default but we'll use `4-2-stable` here:
   - make a new directory and `cd` there
   - follow the below formula or simply run it as a script. Note - before building the icommands package, the dev ("devel" in CentOS) and runtime packages must be installed.  
   ```
   #!/bin/bash
   if [[ $(sh -c '. /etc/os-release ; echo $NAME') =~ (Ubuntu|Debian) ]]
   then INSTALL='dpkg -i'; PKG_EXT='deb'; NINJA_BUILD='ninja'
   else INSTALL='rpm -i';  PKG_EXT='rpm'; NINJA_BUILD='ninja-build'
   fi
   git clone http://github.com/irods/irods
   cd irods ; git submodule update --init;  git checkout 4-2-stable
   mkdir ../build_irods ; cd ../build_irods ; 
   git clone http://github.com/irods/irods_client_icommands
   # --- build and install iRODS ---
   cd irods_client_icommands ; git checkout 4-2-stable 
   cd ../ ; for DIR in irods{,_iclient_icommands}; do mkdir build__$DIR ;  done
   cd build__irods ; cmake -DCMAKE_BUILD_TYPE=Debug ../irods -GNinja
   $NINJA_BUILD package
   $INSTALL irods-{dev,runtime}*.$PKG_EXT
   cd ../build__irods_client_icommands
   cmake -DCMAKE_BUILD_TYPE=Debug ../irods_client_icommands -GNinja
   $NINJA_BUILD package
   $INSTALL irods-{*icommand}*.$PKG_EXT
   $INSTALL ../build__irods/irods-{server,database*postgres}*.$PKG_EXT
   ```
Following this, run the prescribed irods setup command line to configure the system.

### Testing / Jenkins

### Source Version Control / Pull Requests / Git "jail"
