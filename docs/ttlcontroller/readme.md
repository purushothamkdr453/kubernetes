"Jobs" is one of the kuberenetes object. Once job is created and completed it stays there forever unless we delete manually.
Instead of deleting manually we can enable feature gate "TTLAfterFinished" which deletes the jobs after their completion based on the TTL specified.

Enabling Feature Gate:
----------------------------

Login into master node

cd /etc/kubernetes/manifests/


add the following line to kube-apiserver.yml & kube-controller-manager.yml files

- --feature-gates="TTLAfterFinished=true"

Note: If you are using minikube then use the below command while starting the cluster.

minikube start --driver=docker --nodes 2 --feature-gates=TTLAfterFinished=true -p learning

you can verify the options using the below command

ps -aux | grep kube-apiserver
ps -aux | grep kube-controller-manager


Create the Job (with out ttlSecondsAfterFinished ):
------------------------------------------------------

kubectl create -f job-without-ttlSecondsAfterFinished.yml

kubectl get jobs

you can check job will stay there forever until you delete manually.

Create the Job(with ttlSecondsAfterFinished ):
--------------------------------------------------------

kubectl create -f job-with-ttlSecondsAfterFinished.yml

Now in the job definition file we have specified ttl as "20" seconds under -> .spec.ttlSecondsAfterFinished

Hence job will be deleted after 20 seconds of job completion automatically.

you can verify the same by running below command.

watch -x kubectl get all

