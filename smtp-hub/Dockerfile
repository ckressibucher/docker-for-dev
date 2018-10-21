FROM debian:9.5

RUN apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get install -y \
  runit rsyslog postfix ca-certificates mutt libsasl2-2 sasl2-bin

COPY runit_bootstrap /usr/sbin/runit_bootstrap

# user 'peter' is used to receive the mails
# (all mails will be delivered to his mailbox)
RUN useradd -m peter && usermod -aG sasl postfix

EXPOSE 465
EXPOSE 25

COPY service /etc/service

ENTRYPOINT ["/usr/sbin/runit_bootstrap"]
