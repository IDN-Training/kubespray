# Prepare Installation Cluster Kubernetes with Kubespray on all node

## set date & time
timedatectl set-timezone Asia/Jakarta
date

## set hostname
hostnamectl set-hostname [hostname] #master/worker

## update & upgrade repository
sudo apt clean all && sudo apt update -y && sudo apt upgrade -y

## create user for deployment
useradd -m -s /bin/bash deploy

## add permission sudoers to user
echo "deploy ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers.d/deploy

## On deployment node
### install sshpass
apt install sshpass

### config ssh
vim /etc/ssh/ssh_config
StrictHostKeyChecking no

### login user deploy
su - deploy

### generate ssh key
ssh-keygen -t rsa

### create file list host
echo -e "192.168.212.1\n192.168.212.2\n192.168.212.3" > hosts

### create file password
echo "password" > pass

### copy ssh key
for hostname in `cat hosts`; do sshpass -f pass ssh-copy-id deploy@${hostname}; done