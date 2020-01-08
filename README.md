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

8. Vagrant Up!

```
...
==> SW1: Starting domain.
==> SW1: Waiting for domain to get an IP address...
==> SW1: Waiting for SSH to become available...
The configured shell (config.ssh.shell) is invalid and unable
to properly execute commands. The most common cause for this is
using a shell that is unavailable on the system. Please verify
you're using the full path to the shell and that the shell is
executable by the SSH user.
```

Disregard the invalid shell error. The management interface is up and ready for SSH connections.

<pre>
$ <b>vagrant ssh SW1</b>
ExtremeXOS
Copyright (C) 1996-2019 Extreme Networks. All rights reserved.
This product is protected by one or more US patents listed at https://www.extremenetworks.com/company/legal/patents/ along with their foreign counterparts.
==============================================================================


Press the &lt;tab&gt; or '?' key at any time for completions.
Remember to save your configuration changes.


EXOS-VM.1 # <b>show version</b>
Switch          : PN:1N2039    SN:123456   Rev 01 BootROM: 1.2        IMG: 30.4.1.2  
PSUCTRL-1       : PN:MEAD      SN:MD1      Rev 01 BootROM: 2.1       
PSUCTRL-2       : PN:MEAD      SN:MD2      Rev 11 BootROM: 2.3       
mouse-usb       : PN:MOUSE     SN:4321     Rev 11 BootROM: 4.3       
floppy-A        :
PSU-1       : Internal PSU-2 PN:1N2039 SN:12345
PSU-2       : Internal PSU-3 PN:1N2039 SN:12345

Image   : ExtremeXOS version 30.4.1.2 by release-manager
          on Sat Nov 16 02:46:36 EST 2019
BootROM : 1.2
Certified Version : EXOS Linux 4.14.123, FIPS fips-ecp-2.0.16

Build Tools Version : exos-x32-sdk-2.5.3.1.0
</pre>

## Debug

To view the telnet session output for the `expect` task:

```
tail -f ~/exos-console.explog
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
