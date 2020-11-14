## Kubernetes cluster creation using Kind


### Pre-requisites:

- Docker
- Go 
- kubectl(required to check the status of cluster or to interact with cluster)

**Installing Go:**

```
wget https://golang.org/dl/go1.15.2.linux-amd64.tar.gz

tar -xzvf go1.15.2.linux-amd64.tar.gz -C /usr/local/

export PATH=$PATH:/usr/local/go/bin/
```

**Installing kubectl:**

```
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x ./kubectl

mv ./kubectl /usr/local/bin/
```

**Installing Kind:**

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.9.0/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/local/bin
```

you can check the version of kind using command "kind version".

### Creating the cluster:

```
kind create cluster
```
the above command creates a single node(master).

you can get the clusters details using the below command.

```
kind get clusters
```

**Note:** Default name of the cluster is `"kind"`. However you can change this using --name option while running "kind create cluster".

For example kind create cluster --name <clustername>

### Deleting cluster:

```
kind delete cluster
```

### Creating Multi-node cluster:

```
kind create cluster --name <cluster-name> --config ./[multinode-cluster.yaml](#https://github.com/purushothamkdr453/kubernetes/blob/main/docs/kind/multinode-cluster.yaml)
```
