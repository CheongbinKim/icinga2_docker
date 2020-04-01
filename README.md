# icinga2_docker

# CentOS 7.7

# Install Docker

yum install -y yum-utils device-mapper-persistent-data lvm2

yum-config-manager —add-repo https://download.docker.com/linux/centos/docker-ce.repo

yum install docker-ce docker-ce-cli containerd.io

# Run icinga2

sudo docker run -ti -p 3081:80 -v /usr/share/icingaweb2 /etc/icingaweb2 icinga/icinga2
