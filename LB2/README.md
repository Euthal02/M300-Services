# Vorgaben von Herrn Berger
Diese Angaben wurden uns von Herrn Berger übergeben. Damit sind wir in der Lage eine VM zu starten, mit Docker schon installiert.
***
## Docker Testumgebung
    +---------------------------------------------------------------+
    ! Container-Engine: Docker                                      !
    +---------------------------------------------------------------+
    ! Gast OS: Ubuntu 16.04                                         !
    +---------------------------------------------------------------+
    ! Hypervisor: VirtualBox                                        !
    +---------------------------------------------------------------+
    ! Host-OS: Windows, MacOS, Linux                                !
    +---------------------------------------------------------------+
    ! Notebook - Schulnetz 10.x.x.x                                 !                 
    +---------------------------------------------------------------+
***
### Beschreibung

Einfache VM mit Docker installiert.

In der VM können folgende Beispiele ausprobiert werden:

* [apache - Apache Web Server](apache/)
* [db - MySQL Datenbank](mysql/)
* [apache4X - Scriptscript welches 4 Web Server Container erstellt](apache4X/)
* [compose - Docker Compose](compose/)
* [dotnet - .NET Entwicklungsumgebung](dotnet/)
* [jenkins - Build Umgebung](jenkins/)
* [microservice - Micro Service mit Node.js](microservice/)

Es muss einmalig die VM mit Docker erstellt und in die VM gewechselt werden:

	vagrant up
	vagrant ssh

Die Beispiele befinden sich, in der VM, im Verzeichnis `/vagrant`. Die Anwahl ist wie folgt:

	cd /vagrant/<Beispiel>

Die VM kann wie folgt verlassen heruntergefahren und gelöscht werden:

	exit
	vagrant halt
	vagrant destroy -f
***
Wir haben jedoch einige Änderungen vorgenommen, da wir den Port 25 nach aussen benötigen.
Daher werden wir ein eigenes Vagrantfile verweden und Herrn Berger übergeben.

# Voraussetzungen
Um unseren Service zu isntallieren, müssen folgende Dinge gegeben sein.
* vagrant muss installiert sein.
* Dieses Repository muss geklont sein.
* Unser Vagrantfile muss ausgeführt werden.

Dadurch werden die benötigten Packete automatisch erstellt.<br>
Zum Beispiel **docker-compose** dies wird gebraucht, damit wir unseren Container mit den benötigten Variabeln starten können.

Anschliessend kann man per

    vagrant ssh

auf die VM zugreifen und unser Dockerfile mit diesem Command ausführen.

    docker build /home/vagrant/sync/ -t mail_server

Dadurch wird nser Image erstellt. Dieses Image wird dann gebraucht, um unseren Container zu erstellen.

# Dockerfile

## Ausfühern
Mit diesem Command können wir aus unserem Image einen Container erstellen.

docker run -d -e MAILNAME=ganzedomain.ch -e MYNETWORKS="127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 0.0.0.0/0" -p 25:25 -p 143:143 mail_server

Dorthin werden die Ports 25 und 143 geroutet.
