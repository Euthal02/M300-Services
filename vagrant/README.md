# LB1 von Marco Kälin
Dies ist die Markdown Dokumentation von Marco Kälin, für die LB11 des Moduls 300.

## Meine Idee für die LB1
Für die LB1 habe ich mir überlegt einen **Mailserver** zu machen. Ich werde mehrere Serverumgebungen mit einem Vagrantfile erstellen, damit man innerhalb meiner Umgebung Mails versenden kann.

Für einen zweiten Weg, wird Nico Knüsel eine ähnliche Umgebung aufbauen, damit auch der Traffic über das *"Internet"* funktioniert.
So in etwa würde unsere Umgebung aussehen:
![alt text](https://thomas-leister.de/images/2016/04/21/mailserver-schema.png "Unser gewolltes Setup")
Beide werden eine solche Umgebung aufbauen. er /etc/hosts werden wir dann in der Lage sein eine Domain zu mappen.

## Vorbereitungen
Um meine VM möglichst sicher zu machen, müssen alle Updates installiert sein, damit keine Sicherheitslücken geöffnt sind. Nach den Aktualisierungen muss oftmals ein Reboot gemacht werden, darum war es wichtig, dass Vagrant die Box rebooten kann und direkt weiter im Vagrantfile rbeiten kann. Dzu musste das vagrant-plugin reload installiert werden, dies erreicht man mit folgendem Command:
```
vagrant plugin install vagrant-reload
```

Anschliessend kann mit der folgenden Option in enem Vagrantfile ein reload gemacht werden: `config.vm.provision :reload`
