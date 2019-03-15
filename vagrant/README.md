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
### Postfix
Bei der eigentlichen Installation mussten wir zuerst herausfinden wie es genau funktioniert zwei VMs zu erstellen. Dies konnte einfach und schnell erledigt werden. Zur gleichen Zeit setzten wir per Hand einen Mail **(SMTP)** Server auf.

Durch die Teamaufteilung konnte das gemacht werden. Der Sinn dahinter war, die Installation einmal durchzugehen und anschliessend in Vagrant Commands umzusetzen.

Anschliessend hatten wir einige Probleme, da der Service bei der Installation eine interaktive Installation drcchführen wollte. Bis wir die Möglichkeit gefunden haben *noninteractive* zu installieren.
https://serverfault.com/questions/143968/automate-the-installation-of-postfix-on-ubuntu
```
If you want this globally:

dpkg-reconfigure debconf
Then configure it to be "noninteractive"

If you just want it for single install run:

DEBIAN_FRONTEND=noninteractive apt-get install PACKAGE
```

Anfangs erschien dies als gute Möglichkeit, anschliesend haben wir uns aber dazu entschieden die debconf selections vorher zu setzen.<br>
```
debconf-set-selections <<< "postfix postfix/mailname string meuthak.ch"
debconf-set-selections <<< "postfix postfix/main_mailer_type string 'Internet Site'"
```
Dadurch konnten wir anschliessend das Config File bearbeiten. Ich habe folgende Settings auf meinen lokalen VMs gesetzt:<br>

| Namen        | Domain          | IP  |
| :-------------:|:-------------:|:-----:|
| mail-server-1      | ganzedomain.ch | 192.168.50.100 |
| mail-server-2      | anderedomain.ch      | 192.168.50.101 |
Falls wir es hinbekommen, zusammen eine Umgebung aufzubauen, wird Nico folgende Namen und Infos haben:

| Namen        | Domain          | IP  |
| :-------------:|:-------------:|:-----:|
| mail-server-10      | nico-domain-1.ch | 10.71.13.18:125 |
| mail-server-20      | nico-domain-2.ch      | 10.71.13.18:225 |
*Seine IP, welche von Herr Berger zugeteilt worden ist, ist die 10.71.13.18*<br>
Auf allen Servern ist ein Mailserver installiert. Folgende Einstellungen müssen gesetzt sein, damit man einen Mail Server einrichten kann.
````
sudo apt-get -y install postfix mailx
sleep 2
sudo sed -i "s;mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128;mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 0.0.0.0/0;g" /etc/postfix/main.cf
echo "# needed config for lookup service
lmtp_host_lookup = native
smtp_host_lookup= native
disable_dns_lookups = yes
ignore_mx_lookup_error = yes" >> /etc/postfix/main.cf
sudo useradd blub
echo "
localhost   ganzedomain.ch" >> /etc/hosts
echo "192.168.50.101   anderedomain.ch" >> /etc/hosts
sudo service postfix restart
````
### Dovecot
firewall deaktivieren
password nicht vergessen

diese eisntellungen setzten
disable_plaintext_auth = no
mail_privileged_group = mail
mail_location = mbox:~/mail:INBOX=/var/mail/%u
userdb {
  driver = passwd
}
passdb {
  args = %s
  driver = pam
}
protocols = " imap"

nicht mail benutzen, thunderbird.
## Sicherheit
Folgende Ports werden zwingend benötigt, damit unser Service funktioniert.
* SMTP: 25
* POP3: 110
* IMAP: 143
* SMTP Secure: 465
* MSA: 587
* IMAP Secure: 993
* POP3 Secure: 995
