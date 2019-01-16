### RR

   - GDB work-a-like
   
   - can be used within graphical environments like [gdbgui](https://gdbgui.com/)
      * gdbgui easy to [install](https://github.com/cs01/gdbgui/blob/master/docs/INSTALLATION.md)


   - instructional links on

      * [building and installing](https://github.com/mozilla/rr/wiki/Building-And-Installing)
      * [using RR under docker](https://github.com/mozilla/rr/wiki/Docker)


   - basic usage under iRODS:
      - install an iRODS server with debug symbols compiled in.
         (*Can insert **rodsLog(LOG_NOTICE,"...%s..",arg,...)** calls to help ascertaining relevant PID later)*
      - as iRODS, do: `~/irodsctl stop`
      - then, also as iRODS: `cd /usr/sbin ; rr record -w /usr/sbin/irodsServer`
      - (as any user) perform the operations requiring troubleshooting
      - again as iRODS, do: `~/irodsctl stop`
      then:
      ```
      rr ps
      rr replay -p <PID> # or if  forked without exec, then "-f <PID>"
      ```

### Valgrind

## usage with iRODS   
```
# as service account
ubuntu:~$ su - irods
$ cd /var/lib/irods ; ./irodsctl stop
$ cd ..         # Because iRODS can't find the server log otherwise

# Wherever you want the output to go- there will be a lot of these probably ( - Alan )
$ valgrind --tool=memcheck --leak-check=full --trace-children=yes \
      --num-callers=200 --time-stamp=yes --track-origins=yes \
      --keep-stacktraces=alloc-and-free --freelist-vol=10000000000 \
      --log-file=$HOME/valgrind_out_%p.txt /usr/sbin/irodsServer
#  Then everything that happens in irodsServer will be valgrinded.
#  ps -ef | grep valgrind -> yields PID(s); valgrind hides the process name

```
