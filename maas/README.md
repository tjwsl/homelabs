# Getting started

1. Edit `lab.yaml` to pull your own SSH key, using any supported method (ssh_import_id or ssh_authorized_keys)

1. Launch a multipass with the provided YAML:
```
multipass launch --name maas --cloud-init lab.yaml --memory 8G --cpus 4 --disk 20G
```

1. Create a MAAS administrator account
```
sudo maas createadmin --username admin --email tj@woodsidelane.net --ssh-import lp:dotj
```

1. Find the IP of the multipass instance and SSH into it as the `ubuntu` user in order to establish a known SSH host for virt-manager later

1. Log in to the MAAS WebUI

1. Enable DHCP on 10.10.10.0/24 (should be presented as the untagged VLAN on fabric-0). **Make sure to correct default gateway from .254 to .1.**
The MAAS machine acts as a NAT gateway for the internal bridge.

1. Add a virsh KVM host with the connection string:
```
qemu+ssh://ubuntu@<MAAS_MULTIPASS_IP>/system
```

1. Create a new VM using `virt-manager` on the host itself or `virt-install` inside the MAAS machine
```
virt-install --name testvm --disk pool=default,size=5 --memory 2048 --vcpus 2 --pxe --network bridge=br0 --osinfo ubuntu24.04 --noautoconsole --boot network,hd
```

# Tests
* Enlist and commission the new VM
* Refresh the KVM Virsh host in MAAS to gain power control
* Deploy and test the new VM
* Try `ssh` into the multipass VM directly, if you imported your SSH key
* Try `ssh` directly from host using something like `ssh -J <ubuntu@multipassIP> ubuntu@<vmIP>` 

# Caveats
* If you change the multipass instance name, you will need to make sure MAAS itself knows about the name, as it defaults to "maas".
* Watch for OOM when you create the test VM, 1x2 is plenty, though 2x4 is better.  The `multipass` command above creates a 4x8 which should be plenty for either.
