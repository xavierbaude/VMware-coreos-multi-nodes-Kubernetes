# VMware-coreos-multi-nodes-Kubernetes
Create a Kubernetes Cluster on VMware ESXi with CoreOS.

# Prerequisites
* VMware ESXi
  * (optional) a DRS cluster with VCenter for high-availability host.
* DHCP server
* A VMware datastore
* vSphere
* Attention: This requires at least CoreOS version 695.0.0, which includes etcd2.

# CoreOS on VMware
Based on official documentation : https://coreos.com/os/docs/latest/booting-on-vmware.html

Download the OVA, on your local computer :

    curl -LO http://stable.release.core-os.net/amd64-usr/current/coreos_production_vmware_ova.ova

Import ova on VMware via the vSphere Client :

    in the menu, click "File > Deploy OVF Template..."
    in the wizard, specify the location of the OVA downloaded earlier
    name your VM
    confirm the settings then click "Finish"

Create a template via vSphere Client :

    right click on the VM and Template > Convert into template

Now you can create -at least- 2 servers based on this template, do this task but don't start it yet.

# Cloud-Config for master node
On the VMware datastore, create a directory and initialize config, example :

    mkdir -p <path to datastore>/cloud-config/master/openstack/latest/ 
    cd <path to datastore>/cloud-config/master/openstack/latest/
    wget https://github.com/kubernetes/kubernetes/blob/master/docs/getting-started-guides/coreos/cloud-configs/master.yaml && mv master.yaml user_data

Don't forget to add your ssh_key :

	vi user_data
  	[...]
	 ssh_authorized_keys:
	 - ssh-rsa AAAAB...
	  coreos:
	    etcd2:
	  [...]
  
/!\ Finaly, replace all "$private_ipv4" pattern with the ip of master node. The only way to perform this is to fix a DHCP lease with the MAC address of your master server. This MAC address can be get on vsphere : right click on VM, network adapter. Here, 10.0.0.1 is the master fixed ip address.

	sed -i 's|$private_ipv4|10.0.0.1|g' user_data

This is the limitation with coreOS and VMware : https://coreos.com/os/docs/latest/booting-on-vmware.html#cloud-config

Last step : create an iso :

	cd <path to datastore>/cloud-config/
	mkisofs -R -V config-2 -o config-master.iso master/
	
	
# Cloud-Config for minion node
On the VMware datastore, create a directory and initialize config, example :

	mkdir -p <path to datastore>/cloud-config/minion/openstack/latest/
	cd <path to datastore>/cloud-config/minion/openstack/latest/
	wget https://github.com/kubernetes/kubernetes/blob/master/docs/getting-started-guides/coreos/cloud-configs/node.yaml && mv node.yaml user_data
  
 Don't forget to add your ssh_key :
 
	vi user_data
	[...]
  	ssh_authorized_keys:
  	  - ssh-rsa AAAAB...
  	coreos:
	  etcd2:
	[...]

Finaly, replace all "<master-private-ip>" pattern with the ip of master node. (here 10.0.0.1)

	sed -i 's|<master-private-ip>|10.0.0.1|g' user_data

Last step : create an iso :

	cd <path to datastore>/cloud-config/
	mkisofs -R -V config-2 -o config-minion.iso minion/
	
# Start the cluster
On firt VM, mount the config-master.iso with VM properties (CD/DVD reader and "Datastore ISO file"), browse to "<path to datastore>/cloud-config/". Don't foget to set "Connect on Start up".

On second, and all other futher nodes  mount the config-minion.iso.

Start your servers.

# Check 
Check your cluster heatlh : http://10.0.0.1:8080/static/app/#/dashboard/

Check each server :

	ssh core@10.0.0.1
	journalctl -f
	
# Enjoy
