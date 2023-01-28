# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"

  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 2
  end
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.define "pam" do |pam|
    pam.vm.network "private_network", ip: "192.168.50.10"
    #pam.vm.network "forwarded_port", guest:80, host:8080
    pam.vm.hostname = "pam"
    pam.vm.provision "shell", inline: <<-SHELL
        sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
        systemctl restart sshd.service
      SHELL
    
  end


end
