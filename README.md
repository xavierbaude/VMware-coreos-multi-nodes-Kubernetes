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

    right click on the VM and Templte > Convert into template

# Cloud-Config
On the VMware datastore, create a directory and initialize config, example :
Master:

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
  
Minion:

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
