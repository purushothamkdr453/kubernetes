# Minikube installation steps:


### Downloading minikube

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
>   && chmod +x minikube

chmod +x minikube

mv ./minikube /usr/local/bin/
```

### Start the minikube

```
minikube start --vm-driver=none
```
to install specific version of kubernetes run the below command
```
minikube start --vm-driver=none --kubernetes-version v1.19.0
```

### Checking the Status of minikube

```
minikube status
```

### Stop the cluster

```
minikube stop
```

### Deleting the cluster

```
minikube delete
```

### Checking addons list

```
minikube addons list
```

### Enabling Dashboard

```
minikube addon enable dashboard
```

### Fetch the minikube dashboard URL

```
minikube dashboard --url
```

Enabling metrics-server addon

```
minikube addon enable metrics-server
```

### Creating multinode cluster using minikube

```
minikube start --nodes 2 --cpus 2 -p multinode
```

### To switch to a new profile

minikube profile <profile>
```
minikube profile multinode
```

**Note:** Refer to step-10 for starting the cluster using different profile

### Login into Master node

```
minikube ssh
```

### Login into Worker node
```
minikube ssh -n <workernode-name>
```
