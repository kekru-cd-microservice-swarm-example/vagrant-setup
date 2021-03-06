# vagrant-setup
Vagrant setup for the example microservice project

# Installation  
Das Setup der Test- und Produktivumgebung ist automatisiert über eine Vagrant-Konfiguration durchführbar.  
Führen Sie folgende Schritte durch:  

+ Ihr Computer sollte mindestens 10 bis 16 GB RAM und eine schnelle Internetverbindung zur Verfügung haben.
+ Installieren Sie [VirtualBox](https://www.virtualbox.org/wiki/Downloads) oder eine andere mit Vagrant kompatible Virtualisierungslösung. Falls Sie nicht VirtualBox verwenden, müssen ggfs. Anpassungen im Vagrantfile vorgenommen werden.	
+ Installieren Sie [Vagrant](https://www.vagrantup.com/downloads.html).	
+ Führen Sie `vagrant plugin install vagrant-hosts` aus, um das Vagrant Plugin vagrant-hosts zu installieren.
+ Kopieren Sie dieses Git Repository auf Ihren lokalen Computer  
  `git clone https://github.com/kekru-cd-microservice-swarm-example/vagrant-setup.git`
+ Wechseln Sie in das Verzeichnis vagrant-setup
+ Führen Sie `vagrant up --provision` aus.
+ Die VMs werden auf den IP Adressen `10.1.6.210` bis `10.1.6.214` erzeugt.

Der erste Start wird 10 bis 15 Minuten dauert. Es werden die VMs manager1, worker1, worker2, prodmanager1 und prodworker1 erstellt.

# Vagrant Kommandos
+ `vagrant provision`: Aktualisiert die Installationen innerhalb der VMs
+ `vagrant reload`: Aktualisiert die Konfiguration der VMs
+ `vagrant up`: Startet die VMs, nachdem Sie gestoppt, oder gelöscht wurden.
+ `vagrant suspend`: Sichert den aktuellen Zustand der VMs und stoppt sie.
+ `vagrant halt`: Fährt die VMs herunter
+ `vagrant destroy`: Löscht die VMs komplett.

Jede Operation kann auch auf eine einzelne VM ausgeführt werden, z.B. `vagrant provision manager1` 

# Troubleshooting  
+ `vagrant provision` erneuert die Installationen innerhalb der VMs. Das geht relativ schnell (ca. 2 Minuten) und reicht meistens aus.
+ `vagrant reload --provision` führt erst ein Reload der VMs durch, was u.a. die Netzwerkeinstellungen beinhaltet. Anschließend wird die installierte Software erneuert. (Dauer ca. 3 Minuten)
+ `vagrant destroy` löscht die VMs komplett. Das neu Aufsetzen mit `vagrant up` dauert relativ lange (Ca. 10 bis 15 Minuten).
+ Falls Ihr Rechner nicht genügend Leistung aufweißt, können Sie einzelne Worker herunterfahren werden, z.B. mittels `vagrant halt worker1`.  
  
Da die Nutzdaten von Jenkins, der Registry und Redis auf im Vagrant-Shared Folder liegen, in den VMs unter `/vagrant/vm-data`, auf dem Hostrechner im Verzeichnis `vmdata` neben dem `Vagrantfile`, gehen diese beim Neu erstellen der VMs nicht verloren.  
Wenn Sie diese Daten entfernen möchten, löschen Sie das Verzeichnis `vmdata`, das sich neben dem `Vagrantfile` befindet.

# SSH Zugriff
Mit `vagrant ssh manager1` wechseln Sie in eine SSH Session von manager1.  

Unter Windows muss ggfs. ssh.exe zu %PATH% hinzugefügt werden. Wenn Git for Windows installiert ist, kann `C:\Program Files\Git\usr\bin` zu %PATH% hinzugefügt werden.

# Die Beispielanwendung starten
Führen Sie zunächst die obenstehende Anleitung zur Installation durch. anschließend müssen Sie Jenkins und die Pipelines einrichten.  
Dazu gibts es zwei Möglichkeiten.  

## Benutzung der vorbereiteten Installation.
+ Kopieren Sie `vm-data-jenkins-pipelines-konfiguriert.zip`, oder laden Sie die Datei von [https://whiledo.de/sonstiges/bachelorarbeit/vm-data-jenkins-pipelines-konfiguriert.zip](https://whiledo.de/sonstiges/bachelorarbeit/vm-data-jenkins-pipelines-konfiguriert.zip) herunter. Der MD5 Hash der Datei ist `fdbc3bca0dd51e99ec22568235644cfe`.  
+ Entpacken Sie die Datei. Zum Packen wurde 7Zip auf maximaler Kompressionsstufe genutzt. Falls es Probleme beim Entpacken gibt, benutzen Sie bitte auch 7Zip zum Entpacken.  
+ Die Zip Datei enthält einen Ordner `vm-data`.  
+ Löschen Sie zunächst ihren Ordner `vm-data` aus ihrem Arbeitsverzeichnis (das Verzeichnis, wo das Vagrantfile liegt) und kopieren Sie `vm-data` aus der Zip-Datei in ihr Arbeitsverzeichnis.  
+ Anschließend führen Sie `vagrant provision manager1` aus.
+ Der Login für Jenkins ist `admin`, Passwort `admin`.

## Alternativ können Sie Jenkins manuell einrichten.  
Jenkins installieren
+ Loggen Sie sich mit `vagrant ssh manager1` auf manager1 ein.
+ Lesen Sie mit `docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword` das initiale Jenkins-Passwort aus.
+ Öffnen Sie [http://10.1.6.210:8080/](http://10.1.6.210:8080/)
+ Geben Sie das ausgelesene initiale Jenkins-Passwort ein.
+ Wählen Sie `Continue` -> `Install suggested plugins`
+ Legen Sie ein Admin-Konto an, z.B. Login: admin, Passwort: admin

Pipeline einrichten
+ Loggen Sie sich in Jenkins ein [http://10.1.6.210:8080/](http://10.1.6.210:8080/) (admin/admin).
+ Wählen Sie `Element anlegen` -> `Item Name festlegen "newspage"`, `"Pipeline"` auswählen -> OK
+ Wählen Sie unter "Pipeline – Definition": `Pipeline script from SCM`
+ Wählen Sie SCM: `Git`, Repository URL: `https://github.com/kekru-cd-microservice-swarm-example/newspage` -> Speichern
+ Wiederholen Sie diese Schritte für den `commentsservice` mit der Repository URL `https://github.com/kekru-cd-microservice-swarm-example/commentsservice`

Zugangsdaten für Produktiv-Swarm hinzufügen
+ Loggen Sie sich in Jenkins ein [http://10.1.6.210:8080/](http://10.1.6.210:8080/) (admin/admin).
+ Wählen Sie `Zugangsdaten` -> `Jenkins` (in der Tabelle) -> `Globale Zugangsdaten` -> `Zugangsdaten hinzufügen`
+ Nun müssen drei Dateien hinzugefügt werden (die nächsten Schritte dreimal wiederholen).
+ Wählen Sie jeweils Art: `Secret File`, Gültigkeitsbereich: `Global`
+ Erste Datei: File: `vagrant-setup/vm-data/certs-prod/ca-cert.pem`, Id: `prod-ca-cert` -> OK
+ Erste Datei: File: `vagrant-setup/vm-data/certs-prod/client-cert.pem`, Id: `prod-client-cert` -> OK
+ Erste Datei: File: `vagrant-setup/vm-data/certs-prod/client-key.pem`, Id: `prod-client-key` -> OK

# Pipelinedurchlauf starten
+ Loggen Sie sich in Jenkins ein [http://10.1.6.210:8080/](http://10.1.6.210:8080/) (admin/admin).
+ Wählen Sie einen Jenkins-Job (newspage oder commentsservice).
+ Wählen Sie jetzt bauen.
+ Sobald die Pipeline am Schritt `Manuelle Tests` angekommen ist, können Sie die Testumgebung über die angezeigte URL im Jenkins-Log, oder in der Pipelinedarstellung ausprobieren
+ Wählen Sie anschließend `Proceed` im Jenkins-Log, oder in der Pipelinedarstellung
+ Sobald `newspage` das erste Mal erfolgreich durchlaufen ist, ist die Beispielanwendung im Produktivsystem über [http://10.1.6.213/newspage/](http://10.1.6.213/newspage/) erreichbar.
+ In der Navigationsleiste finden Sie zwei Links um Testdaten für Newspage und Commentsservice zu erzeugen.

# Docker und Swarm Visualisierung
+ Unter [http://10.1.6.210:8081/](http://10.1.6.210:8081/) kann der Swarm Visualizer (UI für die Verteilung von Containern im Swarm) für die Testumgebung aufgerufen werden.
+ Unter [http://10.1.6.210:8082/](http://10.1.6.210:8082/) kann für die Testumgebung Portainer aufgerufen werden. Das ist eine UI für Docker, mit der die aktuell laufenden Services und Container betrachtet werden können.
+ Unter [http://10.1.6.213:8081/](http://10.1.6.213:8081/) kann der Swarm Visualizer für die Produktivumgebung aufgerufen werden.

# Authorization Plugin  
Ich probiere aktuell aus, wie man eigene authorization plugins einbinden kann.  
[Gist-Eintrag](https://gist.github.com/kekru/b9e4da822514df93e6fdf2f7d3d90d8a)  
[docker-auth.js](https://github.com/kekru/docker-auth.js)
