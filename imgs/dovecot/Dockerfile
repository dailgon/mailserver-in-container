FROM ronmi/ynit-image:stretch

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
        dovecot-imapd dovecot-lmtpd dovecot-mysql dovecot-pop3d dovecot-sieve \
	spamc rsyslog iptables fail2ban \
 && apt-get clean -y \
 && rm -fr /var/lib/apt/lists/*
 
# make fail2ban runs after dovecot
RUN sed -i 's/# Required-Start:.*/# Required-Start: dovecot/' /etc/init.d/fail2ban
 
RUN addgroup --gid 5000 vmail \
 && adduser --home /home/vmail --no-create-home --uid 5000 --gid 5000 \
            --disabled-login --disabled-password --gecos ,,,,, \
	    vmail

ADD dovecot /etc/dovecot/
RUN echo 'postmaster_address = root@localhost' | tee -a /etc/dovecot/dovecot.conf

ADD sieve /usr/lib/dovecot/sieve/
ADD sieve-pipe /usr/lib/dovecot/sieve-pipe/
RUN sievec /usr/lib/dovecot/sieve/report-spam.sieve \
 && sievec /usr/lib/dovecot/sieve/report-ham.sieve \
 && sievec /usr/lib/dovecot/sieve/move-spam.sieve

ADD dump.sh /etc/ynit/
ADD dump.conf /etc/rsyslog.d/
ADD jail.conf /etc/fail2ban/jail.d/defaults-debian.conf
RUN ln -sf /etc/init.d/dovecot /etc/init.d/rsyslog /etc/init.d/fail2ban /etc/ynit/

CMD ["ynit"]
