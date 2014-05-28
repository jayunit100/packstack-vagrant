VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos65-x86_64-20140116"

  config.vm.box_url = "https://github.com/2creatives/vagrant-centos/releases/download/v6.5.3/centos65-x86_64-20140116.box"

  $script = <<SCRIPT
    sudo ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
    sudo cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    sudo yum update -y

    sudo yum install -y http://rdo.fedorapeople.org/rdo-release.rpm
    sudo yum install -y openstack-packstack

    sudo packstack --allinone

    sudo sed -i 's/^libvirt_type=kvm/libvirt_type=qemu/g' /etc/nova/nova.conf

    date | sudo tee /etc/vagrant_provisioned_at
SCRIPT

  config.vm.provision "shell", inline: $script
end
