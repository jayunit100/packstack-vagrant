VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos65-x86_64-20140116"

  config.vm.box_url = "https://github.com/2creatives/vagrant-centos/releases/download/v6.5.3/centos65-x86_64-20140116.box"

  config.vm.hostname = 'packstack.dev'

  config.vm.network "private_network",  ip: "192.168.50.4"

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 4
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
    config.cache.synced_folder_opts = {
      type: :nfs,
      mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
    }
  end

  $script = <<SCRIPT
    # packstack sshes back into the vagrant instance with this key
    sudo ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
    sudo cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    # should be a better (idempotent) way of doing this
    ipaddress=$(/sbin/ifconfig eth0 | grep 'inet addr:' | cut -d: -f2 | awk '{ print $1}')
    echo $ipaddress packstack.dev packstack | sudo tee -a /etc/hosts

    # iptables exits with 6 if this file doesn't exist and the packstack recipes fail
    sudo touch /etc/sysconfig/iptables

    sudo yum update -y

    sudo yum install -y http://rdo.fedorapeople.org/rdo-release.rpm
    sudo yum install -y openstack-packstack vim

    sudo packstack --allinone

    sudo sed -i 's/^libvirt_type=kvm/libvirt_type=qemu/g' /etc/nova/nova.conf
    sudo service openstack-nova-compute restart

    date | sudo tee /etc/vagrant_provisioned_at
SCRIPT

  config.vm.provision "shell", inline: $script
end
