# LB1 von Marco Kälin
Dies ist die Markdown Dokumentation von Marco Kälin, für die LB11 des Moduls 300.

## Meine Idee für die LB1
Für die LB1 habe ich mir überlegt einen **Mailserver** zu machen. Ich werde mehrere Serverumgebungen mit einem Vagrantfile erstellen, damit man innerhalb meiner Umgebung Mails versenden kann.

Für einen zweiten Weg, wird Nico Knüsel eine ähnliche Umgebung aufbauen, damit auch der Traffic über das *"Internet"* funktioniert.
So in etwa würde unsere Umgebung aussehen:
![alt text](https://thomas-leister.de/images/2016/04/21/mailserver-schema.png "Unser gewolltes Setup")
Beide werden eine solche Umgebung aufbauen. er /etc/hosts werden wir dann in der Lage sein eine Domain zu mappen.

### Infos
Um in das Thema hinein zu kommen, haben wir folgende Seite als Infoquelle genutzt:<br>
https://wiki.ubuntuusers.de/Mailserver-Einf%C3%BChrung/

Dieses Bild dient zur Visualiersierung, was alles installiert werden muss.
![alt text](https://media-cdn.ubuntu-de.org/wiki/attachments/19/30/mail_visual.png "Verbildlichung des gewollten Setups")<br>
Die beiden Mail Clients können von unseren Hosts genutzt werden, da wir schon Mail Programme benutzen. Wir werden also in der Lage sein einen zusätzlichen Account einzurichten und diesen zu benutzen.

Als MTA werden wir **postfix** nutzen. MDA wird voraussichtlicher Weise **Dovecot** sein. Dieser Server unterstützt IMAP und daher auch entsprechend unseren Erwartungen genügend.

### Vorbereitungen
Um meine VM möglichst sicher zu machen, müssen alle Updates installiert sein, damit keine Sicherheitslücken geöffnt sind. Nach den Aktualisierungen muss oftmals ein Reboot gemacht werden.

Darum war es wichtig, dass Vagrant die Box rebooten kann und direkt weiter im Vagrantfile arbeiten kann. Dazu musste das vagrant-plugin reload installiert werden, dies erreicht man mit folgendem Command:
```
vagrant plugin install vagrant-reload
```

Anschliessend kann mit der folgenden Option in enem Vagrantfile ein reload gemacht werden: `config.vm.provision :reload`

## Installation
Bei der eigentlichen Installation mussten wir zuerst herausfinden wie es genau funktioniert zwei VMs zu erstellen. Dies konnte einfach und schnell erledigt werden. Zur gleichen Zeit setzten wir per Hand einen Mail **(SMTP)** Server auf. Durch die Teamaufteilung konnte das gemacht werden. Der Sinn dahinter war, die Installation einmal durchzugehen und anschliessend in Vagrant Commands umzusetzen.
