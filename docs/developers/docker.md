
  - http://docker.com - admin / doc site
  - http://hub.docker.com for software repo


examples

```
  $docker run --name  node_ctnr --rm -it node:8    pull (download) and run nodeJS container, dispose upon exit (--rm)
    (^P ^Q) -> detach

  $ docker attach  node_ctnr
    (^D) -> exit

  $ docker run -it node:8 /bin/bash   
  # npm install mkdirp

  # npm install npm --global             # interact with container
  # npm install mkdirp
  # node
  > mkdirp = require ('mkdirp')
  > mkdirp ('/tmp/abc/def/ghi',function(err){})

  $ docker pull ubuntu:16.04
  $ docker run -dit ubuntu:16.04          # start container in background (-d)
  b3e... <container-SHA prints here> ...
  $docker stop b3e
  $ docker start -ia b3e                  # restart container from the CMD or ENTRYPOINT, remain in foreground
  ^D  or exit                             # stop container
```
___

useful aliases (source [link](https://forums.docker.com/t/how-to-delete-cache/5753))
```
alias docker_clean_images='docker rmi $(docker images -a --filter=dangling=true -q)'
alias docker_clean_ps='docker rm $(docker ps --filter=status=exited --filter=status=created -q)'
```
