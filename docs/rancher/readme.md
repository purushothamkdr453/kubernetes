# Rancher Setup

- [Rancher Setup](#rancher-setup)
   - [Overview](#overview)
   - [Pre-requisities](#pre-requisites)
   - [Installation Rancher using docker](#installation-rancher-using-docker)
   - [Adding cluster to Rancher](#adding-cluster-to-rancher)
   
### Rancher Setup

###  Overview

Rancher is a unified cluster management tool used to manager multiple clusters from differen vendors.
For example 1 cluster from AWS, 1 from Azure, 1 from your on-prem. All can be managed through rancher.


### Pre-requisities

- Docker

**Note** Refer to [docker docs](https://docs.docker.com/get-docker/) for installing docker.


### Installation Rancher using docker

Makesure you have docker installed on a system where you are going to install Rancher.

```
docker run -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/rancher
```
Rancher is installed and Rancher UI can be access through http://<rancher-server-ip>

<rancher-server-ip> -> ip of the server where rancher is installed

Default username is -> admin

First update the password by visiting https://<rancher-server-ip>/update-password


**Resetting administrator password:**

```
docker exec -ti 370104a27cb9 reset-password
```

### Adding cluster to Rancher

Go to Clusters option in Rancher UI -> Click "Add Cluster"

Select Import an existing cluster -> Provide the cluster name

for example -> dev

then Run the commands shown on the page

These commands create a new namespace called "cattle-system" and deploys resouces inside it.

you can verify the same using -> kubectl get all -n cattle-system.


