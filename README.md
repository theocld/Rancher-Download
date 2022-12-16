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

* Copy and paste the following code into a text editor; replace the
values of each <IP_NODE_X> by the IP addresses of your 3 kubernetes nodes
; finally save the file renaming it nginx.conf:

```
load_module /usr/lib/nginx/modules/ngx_stream_module.so;
worker_processes 4;
worker_rlimit_nofile 40000;
events {
worker_connections 8192;
}
stream {
upstream rancher_servers_http {
least_conn;
server &lt;IP_NODE_1&gt;:80 max_fails=3 fail_timeout=5s;
server &lt;IP_NODE_2&gt;:80 max_fails=3 fail_timeout=5s;
server &lt;IP_NODE_3&gt;:80 max_fails=3 fail_timeout=5s;
}
server {
listen 80;
proxy_pass rancher_servers_http;
}
upstream rancher_servers_https {
least_conn;
server &lt;IP_NODE_1&gt;:443 max_fails=3 fail_timeout=5s;
server &lt;IP_NODE_2&gt;:443 max_fails=3 fail_timeout=5s;
server &lt;IP_NODE_3&gt;:443 max_fails=3 fail_timeout=5s;
}
server {
listen 443;
proxy_pass rancher_servers_https;
}
}
```

* Copy nginx.conf file to /etc/nginx/ folder

* Charger les mises à jour en relançant le service nginx. Exécuter ces
commandes dans le terminal :
```bash
sudo service nginx restart
sudo nginx -s reload -t
```

## Install Rancher
If all the prerequisites have been met, installing Rancher is a formality. You
you will need to import the helm directory containing the rancher installation charts,
install a certificate manager and finally start the installation by specifying as
sets the IP address of our load balancer. First, just follow
the [documentation](https://docs.ranchermanager.rancher.io/pages-for-subheaders/install-upgrade-on-a-kubernetes-cluster) of the site up to step 5 not included, with all
necessary commands. 

Once the helm directory has been imported and cert manager has been installed, a final command
is therefore to be launched before accessing the platform:
```bash
helm install rancher rancher-<CHART_REPO>/rancher \
--namespace cattle-system \
--set hostname=<IP_LoadBalancer>.sslip.io \
--set bootstrapPassword=admin
```
Replace <CHART_REPO> by desired Rancher version: latest, stable, or
alpha.
Replace <IP_LoadBalancer> by the public IP of your load balancer machine. This
address will be used to access the Rancher UI and cannot be changed.
Execute the command and wait a bit. You can watch live
deployment using the command:
```bash
kubectl -n cattle-system rollout status deploy/rancher
```

If everything went well, an https address to access the interface
user of your rancher installation will be given to you. All you have to do is
get there!
