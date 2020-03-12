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


---
## Code
```
+--------------------+          
! Web Server         !          
! Host: web01        !          
! IP: 172.16.17.11/24!
! Port: 80           !          
! Nat: 8080          !          
+--------------------+          

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
  config.vm.synced_folder ".", "/var/www/html"  
config.vm.provider "virtualbox" do |vb|
  vb.memory = "512"  
end
config.vm.provision "shell", inline: <<-SHELL
  # Packages vom lokalen Server holen
  # sudo sed -i -e"1i deb {{config.server}}/apt-mirror/mirror/archive.ubuntu.com/ubuntu xenial main restricted" /etc/apt/sources.list 
  sudo apt-get update
  sudo apt-get -y install apache2 
SHELL
end

```
