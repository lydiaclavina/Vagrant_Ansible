# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANT_API_VERSION="2"

CONTROL_IP	="192.168.33.10"
WEB_IP		="192.168.33.101"
DB_IP		="192.168.33.102"

Vagrant.configure(VAGRANT_API_VERSION) do |config|

config.vm.box_check_update = true
  config.vm.boot_timeout = 600
  config.vm.communicator = "ssh"
  config.ssh.username="vagrant"
  config.ssh.insert_key=false

config.vm.define "web" do |web|
	web.vm.box="centos/7"
	web.vm.hostname='web'
	web.vm.network "public_network", ip: WEB_IP
	web.vm.provision "file", source: "ansible_key.pub", destination: "~/.ssh/authorized_keys"
	#web.vm.provision "shell", inline: <<-SCRIPT
      #cat /home/vagrant/.ssh/ansible_key.pub >> /home/vagrant/.ssh/authorized_keys
	  #echo -e "192.168.33.10 control\n192.168.33.101 web\n192.168.33.102 dbserver" >> /etc/hosts
    #SCRIPT
	#web.vm.network "forwarded_port",id: "ssh", guest: 22, host: 22226
  	web.vm.network "forwarded_port",id: "tomcat", guest: 8080, host: 9090
  	#web.ssh.guest_port=22226
end

config.vm.define "dbserver" do |dbserver|
	dbserver.vm.box="centos/7"
	dbserver.vm.hostname='dbserver'
	dbserver.vm.network "public_network", ip: DB_IP
	dbserver.vm.provision "file", source: "ansible_key.pub", destination: "~/.ssh/authorized_keys"
	#dbserver.vm.provision "shell", inline: <<-SCRIPT
      #cat /home/vagrant/.ssh/ansible_key.pub >> /home/vagrant/.ssh/authorized_keys
	  #echo -e "192.168.33.10 control\n192.168.33.101 web\n192.168.33.102 dbserver" >> /etc/hosts
    #SCRIPT
	#dbserver.vm.network "forwarded_port",id: "ssh", guest: 22, host: 22226
  	#dbserver.vm.network "forwarded_port",id: "tomcat", guest: 8080, host: 9090
  	#dbserver.ssh.guest_port=22226
end

config.vm.define "control" do |control|
	control.vm.box = "hashicorp/bionic64"
	control.vm.hostname='control'
	control.vm.network "public_network", ip: CONTROL_IP
	#control.vm.network "forwarded_port",id: "ssh", guest: 22, host: 22224
  	#control.ssh.guest_port=22224

	control.vm.synced_folder ".", "/vagrant", :mount_options => ["dmode=750,fmode=750"]
	control.vm.provision "file", source: "ansible_key", destination: "~/.ssh/ansible_key"
	control.vm.provision "shell", inline: <<-SCRIPT
		apt-get update
    	apt-get install -y ansible
		
		chmod 600 /home/vagrant/.ssh/ansible_key
		
		echo "#{WEB_IP} web1" >> /etc/hosts
		echo "#{DB_IP} db1" >> /etc/hosts
		
		runuser -l vagrant -c 'ssh-keyscan -H web1 >> /home/vagrant/.ssh/known_hosts'
		runuser -l vagrant -c 'ssh-keyscan -H db1 >> /home/vagrant/.ssh/known_hosts'
		#echo "ansible -m ping all" >> /home/vagrant/.bashrc
		runuser -l vagrant -c 'cd /vagrant/playbooks && ansible-playbook application_services.yml'

		#echo "[web]
		#192.168.33.101 ansible_ssh_private_key_file=~/.ssh/ansible_key
		#[db]
		#192.168.33.102 ansible_ssh_private_key_file=~/.ssh/ansible_key" >> /etc/ansible/hosts
		#echo -e "192.168.33.101 web\n192.168.33.10 control\n192.168.33.102 dbserver" >> /etc/hosts
		#cat /home/vagrant/.ssh/ansible_key >> /home/vagrant/.ssh/authorized_keys
		#runuser -l vagrant -c 'ssh-keyscan -H 192.168.33.101 >> /home/vagrant/.ssh/known_hosts'
		#runuser -l vagrant -c 'ssh-keyscan -H 192.168.33.102 >> /home/vagrant/.ssh/known_hosts'
	SCRIPT
	end
end