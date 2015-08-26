# VMware-coreos-multi-nodes-Kubernetes
Create a Kubernetes Cluster on VMware ESXi with CoreOS.

# Prerequisites
* VMware ESXi
  * (optional) a DRS cluster with VCenter for high-availability host.
* DHCP server
* A shared datastore
* vSphere

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
    
# 
