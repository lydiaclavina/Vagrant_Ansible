* Install Virtualbox
* Install Vagrant
* create a keys folder
* Generate ansible ssh keys 
( ssh-keygen -t ed25519 -f keys/ansible_key)
* run vagrant
( vagrant up )
* ssh to control box and run ansible ping
1. vagrant ssh control
2. ansible web -m ping