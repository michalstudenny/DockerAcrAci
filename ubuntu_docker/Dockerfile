FROM ubuntu:latest

EXPOSE 2222

RUN apt update && apt upgrade sudo -y

RUN apt install vim ansible openssh-server sudo -y

RUN mkdir -p /home/ubuntu

COPY setup_env.yml /home/ubuntu

# RUN ansible-playbook --tags ubuntu /home/ubuntu/setup_env.yml
