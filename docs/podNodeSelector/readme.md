using nodeSelector -> we can place the pod to a specific node using labels defined on node.
However there is an another feature i.e any pod we create in specific namespace should to a specific worker nodes.
For example if we create a pod into "Dev" namespace, pods should go to a specific workernodes. 

Enabling "PodNodeSelector" admission plugin:
----------------------------------------------

Login into master node

cd /etc/kubernetes/manifests

vi kube-apiserver.yaml

add the following content to --enable-admission-plugins=<existing-plugins>,PodNodeSelector

if you are using minikube then run the following command while starting the cluster.( No need to edit kube-apiserver.yaml file)

minikube start --nodes 2 --cpus 2 --exra-config=apiserver.enable-admission-plugins=PodNodeSelector -p podnodeselector


Label the nodes:
---------------------------------

kubectl label node <node-name> <key=value>

For example:

kubectl label node podnodeselector-m02 env=dev

creating namespaces:
-------------------------------------------------

Lets create 2 namespaces dev & prod

kubectl create namespace dev
kubectl create namespace prod

Adding an annotation to Namespace:
------------------------------------------------

add the following annotation to the namespace(.metadata.annotations)

scheduler.alpha.kubernetes.io/node-selector: <name-of-node-selector>


kubectl edit namespace dev

add the following

for example:

metadata:
  annotations:
    scheduler.alpha.kubernetes.io/node-selector: "env=dev"

Creating a pod in namespace "Dev":
---------------------------------------------------

kubectl run nginx --image nginx --replicas 2 -n dev

now any pods,deployments,replicasets etc that gets created in "dev" namespace that will be placed on podnodeselector-m02

