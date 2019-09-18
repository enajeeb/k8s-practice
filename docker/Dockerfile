# docker build --rm -t local/k8s-base .
FROM centos:7
ENV container docker
RUN yum -y update; yum clean all
RUN yum -y install systemd; yum clean all; \
(cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;
VOLUME [ "/sys/fs/cgroup" ]
CMD ["/usr/sbin/init"]

# RUN yum -y update; yum clean all

# # install docker
# # https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker

# ### Install required packages.
# RUN yum install -y yum-utils device-mapper-persistent-data lvm2

# ### Add Docker repository.
# RUN yum-config-manager \
#   --add-repo \
#   https://download.docker.com/linux/centos/docker-ce.repo

# ## Install Docker CE.
# RUN yum install -y docker-ce

# RUN usermod -aG docker $(whoami)

# RUN systemctl enable docker.service
# RUN systemctl start docker.service