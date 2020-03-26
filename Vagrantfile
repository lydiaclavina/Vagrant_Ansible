Vagrant.configure("2") do |config|
config.vm.define "control" do |control|
	control.vm.box = "ubuntu/trusty64"
	control.vm.hostname='control'
	control.vm.network "private_network", ip: "192.168.33.10"

	control.vm.provision "file", source: "ansible_key", destination: "~/.ssh/ansible_key"
	#config.vm.synced_folder ".", "/vagrant",  :mount_options => ["dmode=755,fmode=755"]
	control.vm.provision "shell", inline: <<-SHELL
    	apt-get update
    	apt-get install -y ansible
    	SHELL
	#control.vm.provision "shell", path: "setup_ansible.sh"
	#control.vm.synced_folder ".", "/home/vagrant/.ssh/ansible_key", :nfs => true
	control.vm.provision "shell", inline: <<-SCRIPT
		#echo "web ansible_host=192.168.33.101 ansible_connection=ssh ansible_ssh_private_key_file=~/.ssh/ansible_key" >> /etc/ansible/hosts
		chmod 600 /home/vagrant/.ssh/ansible_key
		echo "[web]
		192.168.33.101 ansible_ssh_private_key_file=~/.ssh/ansible_key" >> /etc/ansible/hosts
	SCRIPT
end
config.vm.define "web" do |web|
	web.vm.box="ubuntu/trusty64"
	web.vm.hostname='web'
	web.vm.network "private_network", ip: "192.168.33.101"
	web.vm.provision "file", source: "ansible_key.pub", destination: "~/.ssh/authorized_keys"
end
end