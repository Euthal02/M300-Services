FROM panubo/postfix

ENV DEBIAN_FRONTEND noninteractive

RUN printf '\n# needed config for lookup service \nlmtp_host_lookup = native \nsmtp_host_lookup=native \ndisable_dns_lookups = yes \nignore_mx_lookup_error = yes' >> /etc/postfix/main.cf
RUN useradd -m blub -p $(echo "asdf1234" | openssl passwd -1 -stdin)
RUN echo $'localhost   ganzedomain.ch' >> /etc/hosts
RUN echo "10.71.13.18   anderedomain.ch" >> /etc/hosts
RUN newaliases
RUN service postfix restart
RUN apt-get update
RUN apt-get install -y apt-utils
RUN apt-get install -y heirloom-mailx dovecot-core dovecot-imapd
RUN printf 'disable_plaintext_auth = no \nmail_privileged_group = mail \nmail_location = mbox:~/mail:INBOX=/var/mail/%u \nuserdb { \ndriver = passwd \n} \npassdb { \nargs = %s \ndriver = pam \n} \nprotocols = " imap" \nprotocol imap { \nmail_plugins = \" autocreate\" \n} \nplugin { \nautocreate = Trash \nautocreate2 = Sent \nautosubscribe = Trash \nautosubscribe2 = Sent \n}' >> /etc/dovecot/dovecot.conf
RUN service dovecot restart
