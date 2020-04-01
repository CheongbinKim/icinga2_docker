# icinga2_docker + nrpe + nagios plugins

> CentOS 7.7, server(10.254.1.110), kstack(10.254.1.159)

# Install Docker (server)

> yum install -y yum-utils device-mapper-persistent-data lvm2

> yum-config-manager —add-repo https://download.docker.com/linux/centos/docker-ce.repo

> yum install docker-ce docker-ce-cli containerd.io

> systemctl enable docker

> systemctl start docker


## pull jordan/icinga2

> docker pull jordan/icinga2

## Run test

> docker run -p 80:80 -h icinga2 -t jordan/icinga2:latest


## icinga2 web
default auth (icingaadmin : icinga)
> localhost

# Using nrpe (kstack)

## Install nrpe 

> yum install -y gcc glibc glibc-common openssl openssl-devel perl wget

> cd /tmp

> wget --no-check-certificate -O nrpe.tar.gz https://github.com/NagiosEnterprises/nrpe/archive/nrpe-3.2.1.tar.gz

> tar xzf nrpe.tar.gz

> cd /tmp/nrpe-nrpe-3.2.1/

> ./configure --enable-command-args

> make all

> make install-groups-users

> make install

> make install-config

> firewall-cmd --zone=public --add-port=5666/tcp

> firewall-cmd --zone=public --add-port=5666/tcp --permanent

## nrpe configuration

> sed -i '/^allowed_hosts=/s/$/,10.254.1.110/'/usr/local/nagios/etc/nrpe.cfg 

> sed -i 's / ^ dont_blame_nrpe =. * / dont_blame_nrpe = 1 / g'/ usr / local / nagios / etc / nrpe.cfg

## Install nagios plugin 

> cd /tmp

> wget --no-check-certificate -O nagios-plugins.tar.gz https://github.com/nagios-plugins/nagios-plugins/archive/release-2.2.1.tar.gz

> tar zxf nagios-plugins.tar.gz

> cd /tmp/nagios-plugins-release-2.2.1/

> ./tools/setup

> ./configure

> make

> make install

## start nrpe
> cd /usr/local/nagios/bin
> ./nrpe -c ../etc/nrpe.cfg -d

> cd /usr/local/nagios/libexec
> ./check_nrpe -H 127.0.0.1
> NRPE v3.2.1

> ./check_nrpe -H 127.0.0.1 -c check_sda1
> DISK OK - free space: /boot 834 MB (82.33% inode=100%);| /boot=179MB;811;912;0;1014


# icinga2 configuration (server)

> docker exec -it  [CONTAINER ID] /bin/bash

> icinga2# apt-get update

> icinga2# apt-get install vim

## nrpe command registry

## new host
<pre>
<code>
icinga2# vi /etc/icinga2/conf.d/kstack.cfg
object Host  "kstack"{
  import "generic-host"
  address = "10.254.1.159"
  vars.os = "Linux"
}
</code>
</pre>


## new service
<pre>
<code>
object Service "nrpe-load"{
  import "generic-service"
  
  host_name = "kstack"
  
  check_command = "nrpe"
  vars.nrpe_command = "check_load"
}
</code>
</pre>

## reload icinga2
> service icinga2 restart



