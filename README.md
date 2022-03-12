# kubectl-exec:
kubectl-exec is a bash script that allows you to access a node. it works on both Linux and windows with ssh enabled.

> :warning: **The stable version of kubectl-exec is a small bash script that you can find in this repository. kubectl-exec is now rewritten in Go which has better performance and error handling. It should work the same way (and we'll keep the bash-based implementation around), but we will add new features also to the new Go program. Please help us test this new Go implementation by downloading the binaries from https://github.com/mohatb/kubego.**

# How it works ?

It works by creating a pod (with a priviledged container) in the node you specified and using nsenter http://man7.org/linux/man-pages/man1/nsenter.1.html for getting a shell into your kuberntes nodes.

The created pod is from alpine official image which is ~2.6 mb in size, once you exit the shell, the pod will be deleted.

[![asciicast](https://asciinema.org/a/AtiTZTs319wNFa8yYYMFbcSSd.svg)](https://asciinema.org/a/AtiTZTs319wNFa8yYYMFbcSSd)

# For Windows Nodes:

It is required to have OpenSSH on windows, with credentials user/pass. SSH keys are currently not supported but will be added soon/

https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse

Note: AKS nodes have OpenSSH installed by default.


# Installation:
```
wget https://github.com/mohatb/kubectl-exec/raw/master/kubectl-exec

chmod +x ./kubectl-exec

sudo mv ./kubectl-exec /usr/local/bin/kubectl-exec
```

# Usage:
```
Interavtive
$ kubectl-exec
 
Non-Interactive
$ kubectl-exec NODE
 
Create a mount pod that can be used to transfer data between node and local
$ kubectl-exec -mount
 
Examples:
kubectl-exec
kubectl-exec minikube
kubectl-exec -mount
kubectl-exec -mount node1
```
