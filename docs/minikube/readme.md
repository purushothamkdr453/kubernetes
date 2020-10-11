Minikube installation steps:
----------------------------

Step-1 : Downloading minikube
-----------------------------------------

curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
>   && chmod +x minikube

chmod +x minikube

mv ./minikube /usr/local/bin/


Step-2: Start the minikube
----------------------------

minikube start --vm-driver=none

to install specific version of kubernetes run the below command

minikube start --vm-driver=none --kubernetes-version v1.19.0

step-3: Status of minikube
---------------------------

minikube status


Step-4: Stop the cluster
-------------------------

minikube stop

Step-5: Deleting the cluster
----------------------------

minikube delete

Step-6: Checking addons list
----------------------------

minikube addons list

Step-7: Enabling Dashboard:
-----------------------------

minikube addon enable dashboard

step-8: Fetch the minikube dashboard URL:
----------------------------------------

minikube dashboard --url

step-9: Enabling metrics-server addon:
---------------------------------------

minikube addon enable metrics-server

Step-10: Creating multinode cluster using minikube:
----------------------------------------------------
minikube start --nodes 2 --cpus 2 -p multinode

Step-11: To switch to a new profile:
--------------------------------------

minikube profile <profile>

minikube profile multinode

Note: Refer to step-10 for starting the cluster using different profile

Step-12: Login into Master node:
-----------------------------------

minikube ssh

Step-13: Login into Worker node:
------------------------------------

minikube ssh -n <workernode-name>
