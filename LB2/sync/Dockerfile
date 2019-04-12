FROM debian:stretch

EXPOSE 25 143
ENV DEBIAN_FRONTEND noninteractive
ENV MAILNAME ganzedomain.ch
ENV MYNETWORKS "127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 0.0.0.0/0"


# Preselections for installation
RUN set -x \
  && echo mail > /etc/hostname \
  && echo "postfix postfix/main_mailer_type string Internet site" >> preseed.txt \
  && echo "postfix postfix/mailname string mail.example.com" >> preseed.txt \
  && debconf-set-selections preseed.txt && rm preseed.txt \
  ;

# Install packages
RUN set -x \
  && apt-get update \
  && apt-get install -y apt-utils systemd postfix heirloom-mailx dovecot-core dovecot-imapd mailutils busybox-syslogd opendkim opendkim-tools libsasl2-modules curl ca-certificates procps \
  ;

# Install s6
RUN set -x \
  && S6_VERSION=2.7.1.1 \
  && S6_CHECKSUM=42ad7f2ae6028e7321e2acef432e7b9119bab5fb8748581ca729a2f92dacf613 \
  && EXECLINE_VERSION=2.5.0.0 \
  && EXECLINE_CHECKSUM=f65fba9eaea5d10d082ac75452595958af1f9ca8d298055539597de2f7b713cd \
  && SKAWARE_RELEASE=1.21.5 \
  && curl -sSf -L https://github.com/just-containers/skaware/releases/download/v${SKAWARE_RELEASE}/s6-${S6_VERSION}-linux-amd64-bin.tar.gz -o /tmp/s6-${S6_VERSION}-linux-amd64-bin.tar.gz \
  && curl -sSf -L https://github.com/just-containers/skaware/releases/download/v${SKAWARE_RELEASE}/execline-${EXECLINE_VERSION}-linux-amd64-bin.tar.gz -o /tmp/execline-${EXECLINE_VERSION}-linux-amd64-bin.tar.gz \
  && printf "%s  %s\n" "${S6_CHECKSUM}" "s6-${S6_VERSION}-linux-amd64-bin.tar.gz" "${EXECLINE_CHECKSUM}" "execline-${EXECLINE_VERSION}-linux-amd64-bin.tar.gz" > /tmp/SHA256SUM \
  && ( cd /tmp; sha256sum -c SHA256SUM; ) \
  && tar -C /usr -zxf /tmp/s6-${S6_VERSION}-linux-amd64-bin.tar.gz \
  && tar -C /usr -zxf /tmp/execline-${EXECLINE_VERSION}-linux-amd64-bin.tar.gz \
  && rm -rf /tmp/* \
  ;

# Configure Postfix / dkim
RUN set -x \
  && postconf -e smtpd_banner="\$myhostname ESMTP" \
  && postconf -Me submission/inet="submission inet n - - - - smtpd" \
  && cp -a /var/spool/postfix /var/spool/postfix.cache \
  && rm -f /etc/ssl/private/ssl-cert-snakeoil.key /etc/ssl/certs/ssl-cert-snakeoil.pem \
  && rm -f /etc/opendkim.conf \
  && mkdir /etc/opendkim/ \
  ;

RUN printf '\n# needed config for lookup service \nlmtp_host_lookup = native \nsmtp_host_lookup=native \ndisable_dns_lookups = yes \nignore_mx_lookup_error = yes' >> /etc/postfix/main.cf \
  && useradd -m blub -p $(echo "asdf1234" | openssl passwd -1 -stdin) \
  && usermod -a -G mail blub \
  && useradd -m test -p $(echo "asdf1234" | openssl passwd -1 -stdin) \
  && usermod -a -G mail test \
  && echo $'localhost   ganzedomain.ch' >> /etc/hosts \
  && echo "10.71.13.18   anderedomain.ch" >> /etc/hosts \
  && newaliases \
  && printf 'disable_plaintext_auth = no \nmail_privileged_group = mail \nmail_location = mbox:~/mail:INBOX=/var/mail/%%u \nuserdb { \ndriver = passwd \n} \npassdb { \nargs = %s \ndriver = pam \n} \nnamespace inbox {\ninbox = yes\n  mailbox Trash {\n    auto = subscribe # autocreate and autosubscribe the Trash mailbox\n    special_use = \Trash\n  }\n  mailbox Sent {\n    auto = subscribe # autocreate and autosubscribe the Sent mailbox\n    special_use = \Sent\n}\n}' >> /etc/dovecot/dovecot.conf \
  && chown mail:mail /var/mail \
  && chmod 777 /var/mail \
  && chmod g+s /var/mail \
  ;

COPY header_checks /etc/postfix/header_checks
COPY opendkim.conf.sh /etc/

COPY s6 /etc/s6/
COPY entry.sh /

CMD ["/usr/bin/s6-svscan", "/etc/s6"]
#ENTRYPOINT ["/entry.sh"]
