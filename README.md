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
* 
