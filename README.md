# Rancher-Download
This read.me document presents the prerequisites and the different steps to install the Rancher platform. We focus here on the deployment in a cluster Kubernetes, recommended in production for high availability.

## Prerequisite
* A working kubernetes cluster. This must include 3 linux nodes,
preference of virtual machines. Each of the nodes must have a
ETCD database and a control plane, so form a cluster of 3
masters (HA). Finally, each virtual machine hosting kubernetes must at least
minimum have 2 CPU cores and 5 GB of RAM for the needs of
Rancher.
You can configure and create a custom cluster by following the
instructions on this [link](https://github.com/theocld/rke2-vagrant)

* A separate machine from our cluster, preferably virtual, allowing us
to set up a load balancer (also instantiable from the previous link). This machine must have a static and public IP address.

* Each node in your cluster must be running an Ingress to allow
our load balancer to properly redirect traffic to our machines.
This step is not necessary if the cluster is deployed using RKE2, because
installed by default.

## Load Balancer
The load balancer here is used to direct network traffic to all our Rancher replicas on all
our nodes. The IP address of the machine hosting the load balancer will also be
essential to expose the rancher server in the web and be able to access
the user interface. The next steps will allow you to configure a load
Layer 4 (TCP) balancer in your machine

* Install the NGINX package. The commands differ depending on your version of
[Linux OS](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)
For example, for Ubuntu 20.04, run in your terminal the
following commands:
```bash
sudo apt update
sudo apt install nginx
```

*Copy and paste the following code into a text editor; replace the
values of each <IP_NODE_X> by the IP addresses of your 3 kubernetes nodes
; finally save the file renaming it nginx.conf:

```
yo
```
