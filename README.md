# kubectl-exec:
kubectl-exec is a shell script that uses nsenter http://man7.org/linux/man-pages/man1/nsenter.1.html for getting a shell into your kuberntes nodes.


# Installation:
```
wget https://github.com/mohatb/kubectl-exec/raw/master/kubectl-exec
chmod +x ./kubectl-exec
sudo mv ./kubectl-exec /usr/local/bin/kubectl-exec
```

# Usage:

```
kubectl-exec NODE
```
