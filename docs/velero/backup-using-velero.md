Running minio container:
---------------------------------------------------------------------

docker pull minio/minio
docker run --name minio -p 9000:9000 -v data:/data minio/minio server /data

minio dashboard can be accessed at -> http://localhost:9000

Grab access and secret key

/data/.minio.sys/config/config.json

docker exec -it minio cat /data/.minio.sys/config/config.json | egrep "(access|secret)_key"

Use the access key and secret key to access minio dashboard.

Create the bucket in minio dashboard. for example bucketname as "kubedemo".

Installing Velero:
-----------------------------------------------------

Download Velero 1.0.0 Release

wget https://github.com/vmware-tanzu/velero/releases/download/v1.5.1/velero-v1.5.1-linux-amd64.tar.gz
tar zxf velero-v1.5.1-linux-amd64.tar.gz
sudo mv velero-v1.5.1-linux-amd64/velero /usr/local/bin/
rm -rf velero-v1.5.1-linux-amd64.tar.gz

Create credentials file (Needed for velero initialization)

cat <<EOF>> minio.credentials
[default]
aws_access_key_id=minioadmin
aws_secret_access_key=minioadmin
EOF

Install Velero in the Kubernetes Cluster

velero install \
   --provider aws \
   --bucket kubedemo \
   --plugins velero/velero-plugin-for-aws:v1.0.0 \
   --secret-file ./minio.credentials \
   --backup-location-config region=minio,s3ForcePathStyle=true,s3Url=http://<ip>:9000

The above command creates new namespace "velero" and deploys resources into velero namespace.

kubectl get all -n velero

Lets Create a namespace named "testing" and deploy nginx into testing namespace, then let us velero to take backup of only testing namespace resource.

kubectl create namespace testing

kubectl run nginx --image nginx --replicas 2 -n testing

Take the backup of "testing" namespace resources using velero. run the below command.

velero backup create firstbackup --include-namespaces=testing

backup status details can be checked using -> velero backup describe firstbackup

