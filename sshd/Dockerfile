FROM intra/centos7_base
LABEL maintainer="Rainer Hörbe <r2h2@hoerbe.at>"

RUN yum -y update \
 && yum -y install openssh-server  \
 && yum clean all

# Prepare git ssh service (postpone key generation to run time!)
RUN rm -f /etc/ssh/ssh_host_*_key \
 && mkdir -p /opt/etc/ssh \
 && cp -p /etc/ssh/sshd_config /opt/etc/ssh/sshd_config \
 && echo 'GSSAPIAuthentication no' >> /opt/etc/ssh/sshd_config \
 && echo 'useDNS no' >> /opt/etc/ssh/sshd_config \
 && sed -i -e 's/#Port 22/Port 2022/' /opt/etc/ssh/sshd_config \
 && sed -i -e 's/^HostKey \/etc\/ssh\/ssh_host_/HostKey \/opt\/etc\/ssh\/ssh_host_/' /opt/etc/ssh/sshd_config

EXPOSE 2022

# Need to run as root because of sshd
# starting processes will drop off root privileges
CMD /scripts/start_sshd.sh
