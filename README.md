# microk8striplepicluster
An experiment I'm doing to learn about kubernetes and containerization in general. This takes three raspberry pis (in my case, two raspi 3's and a new 8gb ram raspi 5 all using 64gb sd cards) and attempts to create a cluster to run an application in the same way as larger kubernetes, to learn about the yaml file format and deploying applications.

using this resource as a guide:
https://ubuntu.com/tutorials/how-to-kubernetes-cluster-on-raspberry-pi#4-installing-microk8s
#saved as .html in case it changes in the future

1.) Installing MicroK8s on the pis
Follow this section for each of your Pis. Once completed you will have MicroK8s installed and running everywhere.

SSH into your first Pi and there is one thing we need to do before we get cracking. We need to enable c-groups so the kubelet will work out of the box. To do this you need to modify the configuration file /boot/firmware/cmdline.txt:

sudo nano /boot/firmware/cmdline.txt
And add the following options:

cgroup_enable=memory cgroup_memory=1
The full line for this particular raspberry pi looks like this:

cgroup_enable=memory cgroup_memory=1 net.ifnames=0 dwc_otg.lpm_enable=0 console=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait
Now save the file in your editor and reboot:

sudo reboot
Once that’s done we can now Install the MicroK8s snap:

sudo snap install microk8s --classic
Screenshot%20from%202020-02-21%2010-49-22
What Kubernetes version is this installing?
MicroK8s is a snap and as such it will be automatically updated to newer releases of the package, which is following closely upstream Kubernetes releases.

To follow a specific Kubernetes upstream series it’s possible to select a channel during installation. For example, to follow the v1.17 series:

sudo snap install microk8s --classic --channel=1.17/stable
Channels are made up of a track (or series) and an expected level of stability, based on MicroK8s releases (Stable, Candidate, Beta, Edge). For more information about which releases are available, run:

snap info microk8s
Discovering MicroK8s
Before going further here is a quick intro to the MicroK8s command line:

The start command will start all enabled Kubernetes services: microk8s.start
The inspect command will give you the status of services: microk8s.inspect
The stop command will stop all Kubernetes services: microk8s.stop
To get the status of the cluster: microk8s.kubectl cluster-info
You can easily enable Kubernetes add-ons, eg. to enable “kubedns”: microk8s.enable dns




2.) Deploy app, database, and services on kubernetes once setup:

Deploy the database: Apply the PVC and database deployment YAML files.

kubectl apply -f db-pvc.yaml
kubectl apply -f db-deployment.yaml
kubectl apply -f db-service.yaml

Deploy the app:

Ensure the app deployment YAML includes the database service hostname (e.g., db-service) in the configuration to connect to the database.

kubectl apply -f app-deployment.yaml

