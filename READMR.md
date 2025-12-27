RHEL Linux System Administration Project (RHCSA Aligned)



This project demonstrates RHCSA-level Linux administration on Red Hat Linux (AWS EC2) using only the command line.



🧰 Setup on AWS



1.Open an SSH client.

2.Locate your private key file.

3.Connect to your instance using its Public DNS.

Example:

ssh -i "private key file" User@Public IP or Public DNS

sudo -i      #Become root



Add EBS Volume:

lsblk

fdisk /dev/xvdf        # create partition

mkfs.ext4 /dev/nvme1n1

mkdir /data

mount /dev/nvme1n1/data

echo "/dev/nvme1n1/data ext4 defaults 0 0" >> /etc/fstab



1️⃣ System Information

hostnamectl set-hostname rhel-project

timedatectl

history | tail



2️⃣ User \& Group Administration

useradd -c "This is a RCHA+AWS project" alyfarg -u 1001

echo "alyfarg:admin" >> passwd

groupadd DepOps 

usermod -aG DepOps alyfarg 



\#password aging

chage -m 14 -M 90 -W 7 alyfarg

chage -d 0 alyfarg



\# create group and directory

groupadd DepOps 

mkdir /projects

chgrp DepOps /projects

chmod 2770 /projects      # SGID



3️⃣ Permissions \& Security

chmod 666 /Data/test\*

setfacl -m u:admin1:rwx /projects



4️⃣ Networking

nmcli connection modify ens160 ipv4.addresses 192.168.241.130/24

nmcli connection modify ens160 ipv4.gateway 192.168.241.1

nmcli connection modify ens160 ipv4.dns 8.8.8.8

nmcli connection modify ens160 ipv4.method manual

nmcli connection up ens160



echo "192.168.10.50 fileserver" >> /etc/hosts

ping fileserver



5️⃣ Package Management

yum search nginx

yum install -y nginx

rpm -qa | grep nginx

systemctl enable --now nginx



6️⃣ Backup \& Automation

tar -cvf backup.tar data\*

rsync -av /etc /data/backup



7️⃣ NFS + Automount

yum install -y nfs-utils autofs

systemctl enable nfs-server



echo "/backup\*(rw)" >> /etc/exports

exportfs -rav



\# autofs client

echo "/nfs     /etc/auto.nfs" >> /etc/auto.master

echo "backup   -rw  server:/data" >> /etc/auto.nfs

systemctl restart autofs

ls /nfs/backup



