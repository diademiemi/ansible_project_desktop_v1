# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.nfs.functional = false

    config.vm.define "vagrant_desktop" do |host|
      host.vm.box = "fedora36kde" # See comment below
      host.vm.hostname = "desktop"

      host.vm.network :private_network, ip: "192.168.123.2"
      host.vm.provision :hosts, :sync_hosts => true

      config.vm.provision "shell",
      inline: "ip route add 8.8.8.8/32 dev ens6" # Set ansible_default_ipv4.interface to eth1

      config.vm.provider :libvirt do |libvirt|
          libvirt.driver = "kvm"
          libvirt.memory = 6144
          libvirt.cpus = 4
      end
  end

  config.vm.define "vagrant_laptop" do |host|
    host.vm.box = "fedora36kde" # See comment below
    host.vm.hostname = "laptop"

    host.vm.network :private_network, ip: "192.168.123.3"
    host.vm.provision :hosts, :sync_hosts => true

    config.vm.provision "shell",
    inline: "ip route add 8.8.8.8/32 dev ens6" # Set ansible_default_ipv4.interface to eth1

    config.vm.provider :libvirt do |libvirt|
        libvirt.driver = "kvm"
        libvirt.memory = 6144
        libvirt.cpus = 4
    end
  end

  config.vm.define "vagrant_ubuntu_control" do |host|
    host.vm.box = "kubuntu2204" # See comment below but do it for (K)Ubuntu instead
    host.vm.hostname = "ubuntu-control"

    host.vm.network :private_network, ip: "192.168.123.3"
    host.vm.provision :hosts, :sync_hosts => true

    # config.vm.provision "shell",
    # inline: "ip route add 8.8.8.8/32 dev ens6" # Set ansible_default_ipv4.interface to eth1

    config.vm.provider :libvirt do |libvirt|
        libvirt.driver = "kvm"
        libvirt.memory = 4096
        libvirt.cpus = 2
    end
  end

end

=begin
## STEPS TO GET fedora36kde BOX

###### Replace X with major version in this guide
1. Download Fedora DVD Image (Tested with 36 KDE)  
2. Create a QEMU/KVM libvirt VM (I use virt-manager) 
  Attach this boot image as boot disk.
  I used 20GB as the disk size.
  Name this VM fedoraX 
3. Set installation destination to the local drive
4. configure networking so that you have internet access (Should work out of the box with DHCP)
5. Create a user "vagrant" with password "vagrant"
6. Select minimal installation
7. Proceed with installation
8. After installation is complete, reboot
9. Log in as vagrant user and elevate to root
  `sudo su`
10. Install necessary packages
  `yum install -y openssh wget gcc`
11. Enable SSH server
  `systemctl enable --now sshd`
13. Configure sudoers
  `visudo`
  Add a line `vagrant ALL=(ALL) NOPASSWD: ALL`
14. Shut down the VM
15. Create a directory for the box and navigate to it  
  `mkdir fedoraX && cd fedoraX`
16. Compress the disk image to this directory
  `sudo qemu-img convert -c -O qcow2 /var/lib/libvirt/images/rhelX.Y.qcow2 box.img`
17. Make your user owner of this file
  `sudo chown USER:USER box.img` 
###### Replace USER with your username
18. Create a file `metadata.json` with the content  
```json
{
  "provider"     : "libvirt",
  "format"       : "qcow2",
  "virtual_size" : 20
}
```
19. Create a file Vagrantfile with the content  
```ruby
Vagrant.configure("2") do |config|
  config.ssh.username = "vagrant"
  config.ssh.password = "vagrant"
  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "kvm"
    libvirt.host = 'localhost'
    libvirt.uri = 'qemu:///system'
  end
  config.vm.define "new" do |custombox|
    custombox.vm.box = "fedoraX"       # REPLACE THIS!!!
    custombox.vm.provider :libvirt do |virt|
      virt.memory = 2048
      virt.cpus = 2
    end
  end
end
```
20. Create Vagrant box file
  `tar cvzf fedoraX.box ./metadata.json ./Vagrantfile ./box.img`
21. Import box into Vagrant
  `vagrant box add --name fedoraX fedoraX.box`

You can now use the box with
```ruby
  host.vm.box = "fedoraX"
```
=end