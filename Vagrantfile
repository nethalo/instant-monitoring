# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = false

  config.vm.define "uno" do |uno|
    uno.vm.hostname = "nodo-uno"
    uno.vm.network "private_network", ip: "172.28.128.3"
  end

  config.vm.define "dos" do |dos|
    dos.vm.hostname = "nodo-dos"
    dos.vm.network "private_network", ip: "172.28.128.4"
  end

  config.vm.define "nagioscentral" do |nagioscentral|
     nagioscentral.vm.hostname = "nagios-central"
     nagioscentral.vm.network "forwarded_port", guest: 80, host: 1234
     nagioscentral.vm.network "private_network", ip: "172.28.128.7"
  end

  config.vm.provider "virtualbox" do |vb|
     vb.memory = "512"
  end
end
