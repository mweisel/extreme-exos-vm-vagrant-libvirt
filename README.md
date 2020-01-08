A procedure for creating an Extreme EXOS VM Vagrant box for the [libvirt](https://libvirt.org) provider.

## Prerequisites

  * [Git](https://git-scm.com)
  * [Python](https://www.python.org)
  * [Ansible](https://docs.ansible.com/ansible/latest/index.html)
  * [libvirt](https://libvirt.org) with client tools
  * [QEMU](https://www.qemu.org)
  * [Expect](https://en.wikipedia.org/wiki/Expect)
  * [Telnet](https://en.wikipedia.org/wiki/Telnet)
  * [Vagrant](https://www.vagrantup.com)
  * [vagrant-libvirt](https://github.com/vagrant-libvirt/vagrant-libvirt)

## Steps

0. Verify the prerequisite tools are installed.

```
which git python ansible libvirtd virsh qemu-system-x86_64 expect telnet vagrant
vagrant plugin list
```

1. Clone this GitHub repo and _cd_ into the directory.

```
git clone https://github.com/mweisel/extreme-exos-vm-vagrant-libvirt
cd extreme-exos-vm-vagrant-libvirt
```

2. Download the EXOS-VM_vXX.X.X.X.**qcow2** file from [extremenetworks/Virtual_EXOS](https://github.com/extremenetworks/Virtual_EXOS).

3. Copy (and rename) the disk image file to the `/var/lib/libvirt/images` directory.

```
sudo cp $HOME/Downloads/EXOS-VM_v30.4.1.2.qcow2 /var/lib/libvirt/images/extreme-exos.qcow2
```

4. Modify the file ownership and permissions. Note the owner will differ between Linux distributions. A couple of examples:

> Arch Linux
```
sudo chown nobody:kvm /var/lib/libvirt/images/extreme-exos.qcow2
sudo chmod u+x /var/lib/libvirt/images/extreme-exos.qcow2
```

> Ubuntu 18.04
```
sudo chown libvirt-qemu:kvm /var/lib/libvirt/images/extreme-exos.qcow2
sudo chmod u+x /var/lib/libvirt/images/extreme-exos.qcow2
```

5. Start the `vagrant-libvirt` network (if not already started).

```
virsh -c qemu:///system net-list
virsh -c qemu:///system net-start vagrant-libvirt
```

6. Run the Ansible playbook. 

```
ansible-playbook main.yml
```

7. Add the Vagrant box. 

```
vagrant box add --provider libvirt --name extreme-exos-vm-30.4.1.2 ./extreme-exos.box
```

## Debug

To view the telnet session output for the `expect` task:

```
tail -f ~/exos-console.explog
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
