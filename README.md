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
1. Run `kubectl --kubeconfig ansible/microk8s-kubeconfig get nodes` and you
   should see your cluster's only node.
1. Run `kubectl --kubeconfig ansible/microk8s-kubeconfig get ingress -n awx` to
   find out the hostname configured for the ingress. It will be something like
   `awx.192.0.2.13.sslip.io`, with the VM's IP address in the hostname. This
   always resolves to the IP address in the name.
1. Wait until all pods have started and you see a running `awx-web` pod (with
   3/3 ready containers):

   ```
   $ kubectl --kubeconfig ansible/microk8s-kubeconfig get pods -n awx
   awx-operator-controller-manager-7f44c57b4d-wzxn7   2/2 Running   0  9m58s
   awx-postgres-13-0                                  1/1 Running   0  9m3s
   awx-task-6658c56557-zh4sh                          4/4 Running   0  8m8s
   awx-web-7c774d6754-6vqfl                           3/3 Running   0  6m13s
   $
   ```

1. Open `https://awx.192.0.2.13.sslip.io` in a private browser window and accept
   the self-signed certificate (after checking it, of course). And you should
   see your AWX instance...
1. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install microk8s and AWX (because you want to
install it yourself), just comment out the following lines in the `Vagrantfile`:

```hcl
    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision
```

You also find all of the playbooks in the `ansible` folder.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
