# Preparation for installing PRC (Python iRods Client)
___
## Installing Python and pip (package manager)

To interact with iRODS from a client program written in either Python2.x or 3.x, we can install the PRC
python-irodsclient module 

For Python2.x:
```
ub16:~$ python install pip python2-pip virtualenv
ub16:~$ virtualenv py2env
ub16:~$ . py2env/bin/activate
ub16:~$ pip install python-irodsclient
```

For Python3.x:
```
ub16:~$ python install pip python3-pip python3-venv
ub16:~$ python3 -m venv py3env
ub16:~$ . py3env/bin/activate
ub16:~$ pip install --upgrade pip
ub16:~$ pip install python-irodsclient
```

Note in Ubuntu 14, one should choose to install `python3.4-venv` to accommodate use of virtual environments with the
system python3.4 ( A separate system of packages named according to the pattern `python3.5-*` is available to support  python 3.5.2 )

We can now [install and try out the python irods-client](https://github.com/irods/python-irodsclient#installing) or "PRC"

The introductory examples from this page can be altered for a longer-running application, ie replace the `with` clause by using a more permanent instantiation of the `session` variable
___
## Basic PRC use

```
session = iRODSSession( # ... 
   )
import atexit
   
@atexit.register
def iRODS_session_orderly_destruct ():
    global session
    if session:
        session.cleanup()
        session = None
   ```    

Through the all-important can derive home paths in PRC thus:
```
homePath = "/{}/home/{}".format(session.zone , session.username)
```
then put a file into iRODS and, for example, add metadata:
```
filename = "local.txt"
with open (filename, "w") as local_file : local_file.write("content\n")
logical_path = homePath + "/" + filename
session.data_objects.put( filename , logical_path )
data_obj = session.data_objects.get( logical_path )
data_obj.metadata.add ( iRODSMeta ("val_1", "key_1", "units_1") )
data_obj.metadata.add ( iRODSMeta ("length", "8", "bytes") )
```
