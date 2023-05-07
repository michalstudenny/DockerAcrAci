FROM centos:centos7

EXPOSE 22

RUN yum update sudo -y 

RUN yum install -y epel-release 

RUN yum install openssh-server ansible sudo -y

RUN mkdir /home/centos

COPY setup_env.yml /home/centos

# RUN ansible-playbook --tags centos /home/centos/setup_env.yml

