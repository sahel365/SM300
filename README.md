# Dokumentation SM300 | Sahel Lahoo
## Aufgabe
Wir erstellen - auf Basis von **VirtualBox/Vagrant** - ein selbst gewähltes «Infrastructure as Code»
Projekt, indem wir einen Service oder Serverdienst automatisieren.

Dererstellte Code sowie die gesamte Dokumentation wird versioniert auf GitHub hinterlegt und der Lehrperson zugänglich gemacht.

> Vorgenommene Dienste zum installieren

* Einfacher Webserver
* Nextcloud
* Samba

## Links
[1]: https://bscw.tbz.ch/bscw/bscw.cgi/d31416536/M300_LB2_IaC.pdf "Bewertungskriterien"
[2]: https://wiki.ubuntuusers.de/Serverdienste/ "Dienste"
[3]: https://github.com/mc-b/M300/tree/master/vagrant/web "Webserver"
[4]: https://github.com/mc-b/M300/tree/master/vagrant "Vagrant"
[5]: https://github.com/mc-b/M300/tree/master/20-Infrastruktur#-09---beispiele-f%C3%BCr-lb2 "Kapitel 20"

[Bewertungskriterien][1] 

[Dienste][2] 

Ein einfachs Beispiel zum Anfangen mit einem [Webserver][3] und Port-Weiterleitung ist im Kapitel [Vagrant][4] vorhanden.
Beispiele als Basis für einen Dienst zum automatisieren finden sie in den **Modulunterlagen zum Schluss von [Kapitel 20][5]**

##Inhalt unterstehender .conf Datei

[global]
workgroup = smb
security = user
map to guest = Bad Password

[homes]
comment = Home Directories
browsable = no
read only = no
create mode = 0750

[public]
path = /media/storage/ 
public = yes
writable = yes
comment = smb share
printable = no
guest ok = yes

---
## Code
```
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

#Hier wird ein Vagrant file gemacht und div. Configuration mitgegeben.
 Vagrant.configure(2) do |config|
 #Hier wird z.B. der Box angegeben der online verfügbar ist.
  config.vm.box = "ubuntu/xenial64"
  #Die Port wird weitergeleitet auf 80
  config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
  #Mit der untenstehenden Funktion wird ein Syncordner stellt, der den untenstehenden Ordner synchronisiert. Dort habe ich eine Webseite angelegt und die wurde mir dann angezeigt.
  config.vm.synced_folder "~/Webfiles", "/var/www/html"
  #Hier wird noch gleich der Privider und derren Einstellung der Memory eingegeben.
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "1024" 
  #Zu einem Do gehört natürlich immer ein End
end
#Hier kann man ein Shell Script schreiben und Befehle eingeben. Achtung Sudo su funktioniert heir nicht! Man kann den Script auch in einer separaten Datei angeben und diese danach verlinken. Das würde etwas Ordnung bei grösseren Dateien bringen.
config.vm.provision "shell", inline: <<-SHELL
  sudo apt-get update
  sudo apt-get -y install apache2 
  #apache2 wird installiert anschliessend ist man in der Lage durch port 8080 drauf vom lokalen PC und mit IP von einem anderen Gerät im gleichen Netz drauf zugreifen.
  
  #Hier wird der Shell Befehl beendet. Der Shell darf auch anders heissen, wichtig ist, dass oben und unten das gleiche steht. 
SHELL
end


#Hier wird eine zweite VM erstellt. 
Vagrant.configure(2) do |config|
#Diesmal ein anderes Box aus dem Internet.
  config.vm.box = "javabudd/centos72-samba"
  #Mit SMB Ports
  config.vm.network "forwarded_port", guest:139, host:139, auto_correct: true
    #Hier wird ein Sync Ordner erstellt, damit ich die Conf Datei bearbeiten kann.
  config.vm.synced_folder "~/SMB", "/etc/samba" 
  #Ebenfalls die Provider WM Eisntellungen
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "1024"  
end
  #Hier wird im Shell samba installiert, die .conf Datei gelöscht und neu erstellt, wir können sie danach bearbeiten im Syncordner.
config.vm.provision "shell", inline: <<-SHELL
  sudo apt-get install samba
  sudo mv /etc/samba/smb.conf /etc/samba/smb.orig
  udo vi /etc/samba/smb.conf
SHELL
end

#Mit dem untenstehenden Code wollte ich die Webseite vom lokalen Ordner bereits kopieren zur Virtuellen Mascheni, jedoch habe ich festgestellt, dass dies nicht möglich ist.
#cp -ar ~/Webfiles/ ~/Vagrantprojekte/LB2/

#cp Hier ein dritte Projekt noch, in der der nextcloud box installiert wird.
Vagrant.configure("2") do |config|
  config.vm.box = "ale_polidori/Nextcloud"
  config.vm.box_version = "16.0.1"
  #Mit publich network und fester ip
  config.vm.network "public_network", ip: "192.168.43.161"
  #Da wird der Sync Ordner nochmals mitgegeben.
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provider "virtualbox"
end



```
> Der dritte Code konnte aus zeitlichen Gründen leider nicht erfolgreich getestet werden.
