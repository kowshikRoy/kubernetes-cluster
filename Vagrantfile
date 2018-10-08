# -*- mode: ruby -*-
# vi: set ts=2 sw=2 sts=2 et ft=ruby :

def add_master(config, name, ip)
  config.vm.define name do |node|
    node.vm.box = "bento/ubuntu-16.04"
    node.vm.hostname = name
    node.vm.network "private_network", ip: ip
    node.vm.provision "docker"
    node.vm.provision "shell", path: "vagrant_scripts/master.sh"
    config.vm.provider "virtualbox" do |vb|
      vb.name = name
      vb.memory = "2048"
      vb.cpus = 2
    end
  end
end 


def add_worker(config, name, ip)
  config.vm.define name do |node|
    node.vm.box = "bento/ubuntu-16.04"
    node.vm.hostname = name
    node.vm.network "private_network", ip: ip
    node.vm.provision "docker"
    node.vm.provision "shell", path: "vagrant_scripts/worker.sh"
    config.vm.provider "virtualbox" do |vb|
      vb.name = name
      vb.memory = "1024"
      vb.cpus = 1
    end
  end
end 



Vagrant.configure("2") do |config|
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end
  add_master(config, "master", "192.168.3.3/24")
  add_worker(config, "worker1", "192.168.3.4/24")
  add_worker(config, "worker2", "192.168.3.5/24")
end