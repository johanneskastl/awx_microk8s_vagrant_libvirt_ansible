# awx_microk8s_vagrant_libvirt_ansible

This setup creates a VM with Ubuntu 22.04, installs microk8s and then AWX on top
of it.

It enables the ingress and hostpath-storage addons in microk8s and adds the
vagrant user on the VM to the `microk8s` group.

After the installation it fetches the `kubeconfig` file from the VM and saves it
as `microk8s-kubeconfig` in the `ansible` directory. It modifies the file so it
does no longer contain `127.0.0.1` but rather the VM's actual IP address.

Default OS is Ubuntu 22.04. Although that can be changed in the
Vagrantfile, please beware that this will break the Ansible provisioning.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
1. Fetch the box, per default this is `generic/ubuntu2204`, using
   `vagrant box add generic/ubuntu2204`.
1. Make sure the git submodules are fully working by issuing `git submodule init
   && git submodule update`
1. Run `vagrant up`
1. Wait until the Ansible provisioning has finished. At the end, it should
   output the AWX URL and the credentials.
1. Open the AWX URL in a private browser window and accept the self-signed
   certificate (after checking it, of course). And you should see your AWX
   instance...
1. Party!

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the kubeconfig file (`ansible/microk8s-kubeconfig`) as well as all
files related to the self-signed certificate (`ansible/awx.*`).

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
