#<################################################################################################################
 # Projekt: LinuxSever mit Diensten                                                                              #
 # Modul  : M300                                                                                                 #
 # Autor  : Sahel Lahoo                                                                                          #
 # Datum  : 13.03.2020                                                                                           #
 # Firma  : Entex GmbH                                                                                           #
 # Lehrer : Marco Berger                                                                                         #
 # Version: 1.0                                                                                                  #
 #################################################################################################################
 #>

 Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
  config.vm.synced_folder "~/Webfiles", "/var/www/html"
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "1024"  
end
config.vm.provision "shell", inline: <<-SHELL
  sudo apt-get update
  sudo apt-get -y install apache2 
  

SHELL
end

Vagrant.configure(2) do |config|
  config.vm.box = "javabudd/centos72-samba"
  config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
  config.vm.synced_folder "~/SMB", "/etc/samba" 
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "1024"  
end
config.vm.provision "shell", inline: <<-SHELL
  sudo apt-get install samba
  sudo mv /etc/samba/smb.conf /etc/samba/smb.orig
  udo vi /etc/samba/smb.conf
SHELL
end


#cp -ar ~/Webfiles/ ~/Vagrantprojekte/LB2/

Vagrant.configure("2") do |config|
  config.vm.box = "ale_polidori/Nextcloud"
  config.vm.box_version = "16.0.1"
  config.vm.network "public_network", ip: "192.168.43.161"
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provider "virtualbox"
end

