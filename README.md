# kubectl-exec:
kubectl-exec is a shell script that allows you to get into a node shell.

# How it works ?

It works by creating a pod (with a priviledged container) in the node you specified and using nsenter http://man7.org/linux/man-pages/man1/nsenter.1.html for getting a shell into your kuberntes nodes.

The created pod is from alpine official image which is ~2.6 mb in size, once you exit the shell, the pod will be deleted.

# For windows Nodes:

It is required to have Openssh on windows, with credentials user/pass. SSH keys are currently not supported but will be added soon/

https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse


# Installation:
```
wget https://github.com/mohatb/kubectl-exec/raw/master/kubectl-exec

chmod +x ./kubectl-exec

sudo mv ./kubectl-exec /usr/local/bin/kubectl-exec
```

# Usage:
```
Interactive:
$ kubectl-exec

Non-Interactive
$ kubectl-exec NODE
```
