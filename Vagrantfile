# The following patch needs to be applied for ansible to run cleanly on vagrant up:
# https://github.com/mitchellh/vagrant/pull/1723.diff on /opt/vagrant/embedded/gems/gems/vagrant-1.2.x

$boxes = [
  {
    :name => 'ansible-wordpress.local',
    :forwards => { 22  => 22222,
                   80  => 8080,
    #                25  => 25,
    #                143 => 143,
    #                465 => 465,
    #                993 => 993
    }
  }
]

Vagrant.configure('2') do |config|
  config.vm.provider :lxc do |lxc, override|
    override.vm.box     = 'precise64'
    override.vm.box_url = 'http://bit.ly/vagrant-lxc-precise64-2013-05-08'
  end

  config.vm.provider :virtualbox do |vbox, override|
    override.vm.box     = 'precise64'
    override.vm.box_url = 'http://files.vagrantup.com/precise64.box'
  end

  $boxes.each do |opts|
    config.vm.hostname = opts[:name]
    config.vm.network :private_network, ip: "192.168.33.92"

    opts[:forwards].each do |guest_port, host_port|
      config.vm.network :forwarded_port, :guest => guest_port, :host => host_port
    end unless opts[:forwards].nil?

    config.vm.provision :shell,
                        :inline => 'if [ ! -e /root/apt.updated ]; then apt-get update && touch /root/apt.updated ; fi; apt-get install -y python-apt'

    config.vm.provision :shell,
                        :inline => "echo [test] > /vagrant/hosts.autogen && ifconfig eth1 | grep 'inet addr' | awk '{print $2}' | cut -d: -f2 >> /vagrant/hosts.autogen"

    config.vm.provision :ansible do |ansible|
      ansible.playbook = 'site.yml'
    end
  end
end
