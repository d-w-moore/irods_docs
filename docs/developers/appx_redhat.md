# Alterations to Procedures for RedHat 7 and CentOS 7

RedHat instructions were tested on the Docker image:  `yjjy0921/redhat7.2`

## Repository prerequisites for iRODS dependencies

<a name="centos_repo"> </a>
# Adding a Centos repo under RedHat 7:

```
cat >/etc/yum.repos.d/centos.repo <<'EOF'
[centos]
name=CentOS $releasever - $basearch
baseurl=http://ftp.heanet.ie/pub/centos/7/os/$basearch/ 
enabled=1
gpgcheck=0
EOF
```
<a name="epel_prereq"> </a>
After doing the above, installing the EPEL Release (Extra Packages for Enterprise Linux) - necessary for tig, postgresql, and a number of other software packages - is as easy as : `yum install epel-release`

In RedHat7 the following will accomplish the same.
```
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

---


# Extra instructions for Git Bash-Prompt Annotations for `git` under C7/RH7

If on RH7/Centos7, we will need the following to define `__git__ps1`:
   - do `curl -L https://raw.github.com/git/git/master/contrib/completion/git-prompt.sh > ~/.bash_git`
   - append `source ~/.bash_git` to the `~/.bashrc`,
   - also append to `~/.bashrc` the standard [function definition and invocation](git_annote.md#git_bash) in `~/.bashrc` and the git prompt coloration should work when the BASHRC is next sourced.
( Source Link  https://stackoverflow.com/questions/15384025/bash-git-ps1-command-not-found )

