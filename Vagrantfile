# -*- mode: ruby -*-
# vi: set ft=ruby :
# test

Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/trusty64" # See https://atlas.hashicorp.com/search.

  config.vm.define "datalab" do |virtualbuild|
    virtualbuild.vm.network "private_network", ip: "10.10.10.10"
    virtualbuild.vm.hostname = "datalab"
    config.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "2048"]
    end
  end

  config.vm.provider "virtualbox" do |vb|
  	vb.gui = false # No VirtualBox GUI
	end

end
