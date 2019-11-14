# kubectl-exec:
kubectl-exec is a shell script that allows you to get into a node shell.

# How it works ?
It works by creating a pod (with a priviledged container) in the node you specified and using nsenter http://man7.org/linux/man-pages/man1/nsenter.1.html for getting a shell into your kuberntes nodes.

The created pod is from alpine official image which is ~2.6 mb in size, once you exit the shell, the pod will be deleted.


# Installation:
```
$ wget https://github.com/mohatb/kubectl-exec/raw/master/kubectl-exec

$ chmod +x ./kubectl-exec

$ sudo mv ./kubectl-exec /usr/local/bin/kubectl-exec
```

# Usage:
```
Interactive:
$ kubectl-exec

Non-Interactive
$ kubectl-exec NODE
```

# inspired by:
https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm/
