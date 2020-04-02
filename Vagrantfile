# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANT_API_VERSION="2"

CONTROL_IP	="192.168.33.10"
WEB_IP		="192.168.33.101"
DB1_IP		="192.168.33.102"
DB2_IP		="192.168.33.103"

Vagrant.configure(VAGRANT_API_VERSION) do |config|

config.vm.box_check_update = true
  config.vm.boot_timeout = 600
  config.vm.communicator = "ssh"
  config.ssh.username="vagrant"
  config.ssh.insert_key=false

config.vm.define "web1" do |web1|
	web1.vm.box="centos/7"
	web1.vm.hostname='web1'
	web1.vm.network :private_network, ip: WEB_IP
	web1.vm.provision "file", source: "keys/ansible_key.pub", destination: "~/.ssh/authorized_keys"
	web1.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--memory", 256]
    end
  	web1.vm.network "forwarded_port",id: "tomcat", guest: 8080, host: 8080
end

config.vm.define "db1" do |db1|
	db1.vm.box="centos/7"
	db1.vm.hostname='db1'
	db1.vm.network :private_network, ip: DB1_IP
	db1.vm.provision "file", source: "keys/ansible_key.pub", destination: "~/.ssh/authorized_keys"
	db1.vm.provider :virtualbox do |v|
    	v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    	v.customize ["modifyvm", :id, "--memory", 512]
    end
end

config.vm.define "db2" do |db2|
	db2.vm.box="ubuntu/bionic64"
	db2.vm.hostname='db2'
	db2.vm.network :private_network, ip: DB2_IP
	db2.vm.provision "file", source: "keys/ansible_key.pub", destination: "~/.ssh/authorized_keys"
	db2.vm.provider :virtualbox do |v|
    	v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    	v.customize ["modifyvm", :id, "--memory", 512]
    end
end

config.vm.define "control" do |control|
	control.vm.box = "hashicorp/bionic64"
	control.vm.hostname='control'
	control.vm.network :private_network, ip: CONTROL_IP
	control.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--memory", 512]
      #v.customize ["modifyvm", :id, "--name", "control"]
    end

	control.vm.synced_folder ".", "/vagrant", :mount_options => ["dmode=750,fmode=750"]
	control.vm.provision "file", source: "keys/ansible_key", destination: "~/.ssh/ansible_key"
	control.vm.provision "shell", inline: <<-SCRIPT
		apt-get update
    	apt-get install -y ansible
		
		chmod 600 /home/vagrant/.ssh/ansible_key
		
		echo "#{WEB_IP} web1" >> /etc/hosts
		echo "#{DB1_IP} db1" >> /etc/hosts
		echo "#{DB2_IP} db2" >> /etc/hosts

		runuser -l vagrant -c 'ssh-keyscan -H web1 >> /home/vagrant/.ssh/known_hosts'
		runuser -l vagrant -c 'ssh-keyscan -H db1 >> /home/vagrant/.ssh/known_hosts'
		runuser -l vagrant -c 'ssh-keyscan -H db2 >> /home/vagrant/.ssh/known_hosts'
		
		runuser -l vagrant -c 'cd /vagrant/playbooks && ansible-playbook application_services.yml'
	SCRIPT
	end
end