# vagrant-setup
Vagrant setup for the example microservice project

## Installation  
Das Setup der Test- und Produktivumgebung ist automatisiert über eine Vagrant-Konfiguration durchführbar.  
Führen Sie folgende Schritte durch:  

+ Ihr Computer sollte mindestens 10 bis 16 GB RAM und eine schnelle Internetverbindung zur Verfügung haben.
+ Installieren Sie [VirtualBox](https://www.virtualbox.org/wiki/Downloads) oder eine andere mit Vagrant kompatible Virtualisierungslösung. Falls Sie nicht VirtualBox verwenden, müssen ggfs. Anpassungen im Vagrantfile vorgenommen werden.	
+ Installieren Sie [Vagrant](https://www.vagrantup.com/downloads.html).	
+ Führen Sie `vagrant plugin install vagrant-hosts` aus, um das Vagrant Plugin vagrant-hosts zu installieren.
+ Kopieren Sie dieses Git Repository auf Ihren lokalen Computer  
  `git clone https://github.com/kekru-cd-microservice-swarm-example/vagrant-setup.git`
+ Wechseln Sie in das Verzeichnis vagrant-setup
+ Führen Sie `vagrant up` aus.

Der erste Start wird 10 bis 15 Minuten dauert. Es werden die VMs manager1, worker1, worker2, prodmanager1 und prodworker1 erstellt.

## Vagrant Kommandos
+ `vagrant provision`: Aktualisiert die Installationen innerhalb der VMs
+ `vagrant reload`: Aktualisiert die Konfiguration der VMs
+ `vagrant up`: Startet die VMs, nachdem Sie gestoppt, oder gelöscht wurden.
+ `vagrant suspend`: Sichert den aktuellen Zustand der VMs und stoppt sie.
+ `vagrant halt`: Fährt die VMs herunter
+ `vagrant destroy`: Löscht die VMs komplett.

Jede Operation kann auch auf eine einzelne VM ausgeführt werden, z.B. `vagrant provision manager1` 

## Troubleshooting  
+ `vagrant provision` erneuert die Installationen innerhalb der VMs. Das geht relativ schnell (ca. 2 Minuten) und reicht meistens aus.
+ `vagrant reload --provision` führt erst ein Reload der VMs durch, was u.a. die Netzwerkeinstellungen beinhaltet. Anschließend wird die installierte Software erneuert. (Dauer ca. 3 Minuten)
+ `vagrant destroy` löscht die VMs komplett. Das neu Aufsetzen mit `vagrant up` dauert relativ lange (Ca. 10 bis 15 Minuten).
  
Da die Nutzdaten von Jenkins, der Registry und Redis auf im Vagrant-Shared Folder liegen, in den VMs unter `/vagrant/vm-data`, auf dem Hostrechner im Verzeichnis `vmdata` neben dem `Vagrantfile`, gehen diese beim Neu erstellen der VMs nicht verloren.  
Wenn Sie diese Daten entfernen möchten, löschen Sie das Verzeichnis `vmdata`, das sich neben dem `Vagrantfile` befindet.

## SSH Zugriff
Mit `vagrant ssh manager1` wechseln Sie in eine SSH Session von manager1.  

Unter Windows muss ggfs. ssh.exe zu %PATH% hinzugefügt werden. Wenn Git for Windows installiert ist, kann `C:\Program Files\Git\usr\bin` zu %PATH% hinzugefügt werden.

