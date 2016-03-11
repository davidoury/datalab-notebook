# -*- mode: ruby -*-
# vi: set ft=ruby :
# datalab virtual box

Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/trusty64"

  config.vm.define "datalab" do |virtualbuild|
    virtualbuild.vm.network "private_network", ip: "10.10.10.10"
    virtualbuild.vm.hostname = "datalab"

    config.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "2048"]
    end
  end

  config.vm.network "forwarded_port", guest: 8888, host: 8888

  config.vm.provider "virtualbox" do |vb|
  	vb.gui = false # No VirtualBox GUI
	end
	
	#For ubuntu/trusty64
	config.vm.provision "shell", inline: "echo 1; sudo apt-get update"
	config.vm.provision "shell", inline: "echo 2; sudo apt-get install -y apt-transport-https ca-certificates"
	config.vm.provision "shell", inline: "echo 3; sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D"
	config.vm.provision "shell", inline: "echo 4; sudo echo deb https://apt.dockerproject.org/repo ubuntu-trusty main > /etc/apt/sources.list.d/docker.list"
	config.vm.provision "shell", inline: "echo 5; sudo apt-get update"
	config.vm.provision "shell", inline: "echo 6; sudo apt-get purge lxc-docker"
	config.vm.provision "shell", inline: "echo 7; sudo apt-get update"
	config.vm.provision "shell", inline: "echo 8; sudo apt-get install -y linux-image-extra-$(uname -r)"
	config.vm.provision "shell", inline: "echo 9; sudo apt-get install -y apparmor"
	config.vm.provision "shell", inline: "echo 10; sudo apt-get update"
	config.vm.provision "shell", inline: "echo 11; sudo apt-get install -y docker-engine"
	config.vm.provision "shell", inline: "echo 12; sudo docker run hello-world"
	config.vm.provision "shell", inline: "echo 13; sudo usermod -aG docker vagrant"
	config.vm.provision "shell", inline: "echo 14; sudo docker pull jupyter/pyspark-notebook"
	config.vm.provision "shell", inline: "echo 15; cd /; tar xvf /vagrant/cron.d.tar.gz"
	config.vm.provision "shell", inline: "echo 16; /etc/cron.d/docker.sh"
	
#For Centos
#config.vm.provision "shell", inline: "echo 1; sudo yum update --exclude=nfs-utils -y"
#config.vm.provision "file",  source: "docker.repo", destination: "/tmp/docker.repo"
#config.vm.provision "shell", inline: "sudo cp /tmp/docker.repo /etc/yum.repos.d/docker.repo"
#config.vm.provision "shell", inline: "yum install -y docker-engine"
#config.vm.provision "shell", inline: "service docker start"
#config.vm.provision "shell", inline: "chkconfig docker on"
#config.vm.provision "shell", inline: "sudo usermod -aG docker vagrant"

end
