#!/bin/bash

#Check kubectl client exists
if ! command -v kubectl &> /dev/null
then
    printf "Kubectl client could not be found. Please install Kubectl client before running kubectl-exec.\nRefer to: https://kubernetes.io/docs/tasks/tools/\n\n"
    exit 1
fi

#Check client major/minor versions
kubectl_major_version=$(kubectl version --client -o yaml | grep -i "major" | awk '{ print $2 }' | sed 's/"//g')
kubectl_minor_version=$(kubectl version --client -o yaml | grep -i "minor" | awk '{ print $2 }' | sed 's/"//g')

if [[ $kubectl_major_version -gt 1 ]] || [[ $kubectl_major_version -eq 1 && $kubectl_minor_version -ge 18 ]]
then
        use_generator=false
        echo "Kuberetes client version is $kubectl_major_version.$kubectl_minor_version. Generator will not be used since it is deprecated."
elif [[ $kubectl_major_version -eq 1 && $kubectl_minor_version -lt 18 ]]
then
        use_generator=true
        echo "Kuberetes client version is $kubectl_major_version.$kubectl_minor_version. Generator will be used."
else
        echo "Invalid kubectl version, exiting."
        exit 1
fi

#Usage message
usage()
{
        echo 'Interactive:'
        echo '$ kubectl-exec'
        echo " "
        echo 'Non-Interactive'
        echo '$ kubectl-exec NODE'
        echo " "
        echo "You can mount node root volume to pod with -mount"
        echo "./kubectl-exec -mount"
        echo " "
        echo "Examples:"
        echo "kubectl-exec"
        echo "kubectl-exec minikube"
        echo "kubectl-exec -mount"
        echo "kubectl-exec -mount node1"
        echo " "
        exit 0
}

#windows function to ssh to linux nodes
LINUXNODES () {

    IMAGE="alpine"
    POD="$NODE-exec-$(env LC_CTYPE=C tr -dc a-z0-9 < /dev/urandom | head -c 6)"

    # Check the node existance
    kubectl get node "$NODE" >/dev/null || exit 1

    #nsenter JSON overrrides
    OVERRIDES="$(cat <<EOT
{
  "spec": {
    "nodeName": "$NODE",
    "hostPID": true,
    "containers": [
      {
        "securityContext": {
          "privileged": true
        },
        "image": "$IMAGE",
        "name": "nsenter",
        "stdin": true,
        "stdinOnce": true,
        "tty": true,
        "command": [ "nsenter", "--target", "1", "--mount", "--uts", "--ipc", "--net", "--pid", "--", "bash", "-l" ]
      }
    ]
  }
}
EOT
)"

echo "creating pod \"$POD\" on node \"$NODE\""
if [ $use_generator = true ]
then
        kubectl run --rm --image $IMAGE --overrides="$OVERRIDES" --generator=run-pod/v1 -ti "$POD"
else
        kubectl run --rm --image $IMAGE --overrides="$OVERRIDES" -ti "$POD"
fi
}

#windows function to ssh to windows nodes
WINDOWSNODES () {

echo "SSH into windows VM"
read -p "Enter the windows node ssh user: " WINDOWSSSHUSER
    IMAGE="alpine"
    POD="$NODE-exec-$(env LC_CTYPE=C tr -dc a-z0-9 < /dev/urandom | head -c 6)"

    # Check the node existance and IP
    kubectl get nodes "$NODE" -o wide >/dev/null || exit 1
    WINNODEIP=$(kubectl get node $NODE --output=jsonpath='{.status.addresses[?(@.type=="InternalIP")].address}')

    #nsenter JSON overrrides
    OVERRIDES="$(cat <<EOT
{
  "spec": {
    "hostPID": true,
    "nodeSelector": {
      "kubernetes.io/os": "linux"
      },
    "containers": [
      {
        "securityContext": {
          "privileged": true
        },
        "image": "$IMAGE",
        "name": "nsenter",
        "stdin": true,
        "stdinOnce": true,
        "tty": true,
        "command": [ "nsenter", "--target", "1", "--mount", "--uts", "--ipc", "--net", "--pid", "--", "ssh", "$WINDOWSSSHUSER@$WINNODEIP" ]
      }
    ]
  }
}
EOT
)"

echo "creating pod \"$POD\" for windows ssh"

if [ $use_generator = true ]
then
        kubectl run --rm --image $IMAGE --overrides="$OVERRIDES" --generator=run-pod/v1 -ti "$POD"
else
        kubectl run --rm --image $IMAGE --overrides="$OVERRIDES" -ti "$POD"
fi

}

#function to mount linux root file system to pod
LINUXHOSTMOUNT () {

    IMAGE="alpine"
    POD="$NODE-hostpath-mount"

    # Check the node existance
    kubectl get node "$NODE" >/dev/null || exit 1

    OVERRIDES="$(cat <<EOT
{
  "spec": {
    "hostPID": true,
    "nodeSelector": {
      "kubernetes.io/os": "linux"
      },
    "containers": [
      {
        "securityContext": {
          "privileged": true
        },
        "image": "$IMAGE",
        "volumeMounts": [
            {
              "mountPath": "/host",
              "name": "hostrootvolume",
              "readOnly": false
            }
          ],
        "name": "hostmount",
        "stdin": true,
        "stdinOnce": true,
        "tty": true
      }
    ],
    "volumes": [
      {
        "name": "hostrootvolume",
        "hostPath": {
          "path": "/",
          "name": "hostrootvolume"
        }
      }
    ]
  }
}
EOT
)"

echo "creating pod \"$POD\" on node \"$NODE\""
if [ $use_generator = true ]
then
        kubectl run --image $IMAGE --overrides="$OVERRIDES" --generator=run-pod/v1 "$POD"
else
        kubectl run --image $IMAGE --overrides="$OVERRIDES" "$POD"
fi

}


#check if user is looking for focumentation with -h
if [ "$1" = "-h" ]
then
        usage
        exit 1
fi

#Check user input for shell access
if [ -z "$1" ]; then
        
        mapfile -t nodenumber < <( kubectl get nodes --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}' )

        for i in "${!nodenumber[@]}"; do
          printf "$i ${nodenumber[i]} \n"
        done
        
        read -p "Enter the node number: " NODE_INDEX
        NODE=${nodenumber[NODE_INDEX]}

    else
        NODE=$1
    fi

#Statement to check if user is looking to mount root file system in linux nodes to the pod.
if [ "$1" = "-mount" ] && [ -n "$2" ]; then
#Check if the user provided node number
       NODE="$2"
       LINUXHOSTMOUNT
       exit 1

  elif [ "$1" = "-mount" ] && [ -z "$2" ]; then
#If the user did not provide a node number, use interactive to get it.
		mapfile -t nodenumber < <( kubectl get nodes --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}' )

        for i in "${!nodenumber[@]}"; do
          printf "$i ${nodenumber[i]} \n"
        done
        
        read -p "Enter the node number: " NODE_INDEX
        NODE=${nodenumber[NODE_INDEX]}
        LINUXHOSTMOUNT
        exit 1
fi

#Evaluate if windows node.
NODEOS=$(kubectl get node $NODE -o jsonpath="{.metadata.labels.\kubernetes\.io/os}")

if [ $NODEOS = "windows" ]
then
    WINDOWSNODES
else
    LINUXNODES
fi
