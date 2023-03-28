##  **PROJECT 7 - DEVOPS TOOLING WEBSITE SOLUTION**

## STEP 1 – PREPARATION OF NFS SERVER


### NSF, DATABASE and WEB-servers instance created and EBS volumes were attached

![Instances-creation](./images/Instances-creation.PNG)

### ESB volumes created and attached to NSF server

### Confirming disks created on the NSF server

`lsblk`

![nsf-lsblk](./images/nsf-lsblk.PNG)

### Creation of partitions on NSF server on the disk earlier created using gdisk utility

`sudo gdisk /dev/xvdf`

`sudo gdisk /dev/xvdg`

`sudo gdisk /dev/xvdh`

![nfs-xvdf-partition](./images/nfs-xvdf-partition.PNG)

![nfs-xvdg-partition](./images/nfs-xvdg-partition.PNG)

![nfs-xvdh-partition](./images/nfs-xvdh-partition.PNG)

### Using lsblk utility to confirm the newly configured partitions on the NSF server

`lsblk`

![nfs-lsblk-after-partition](./images/nfs-lsblk-after-partition.PNG)

### Installation of LVM package

`sudo yum install lvm2 -y`

![nfs-lvm-installation](./images/nfs-lvm-installation.PNG)

### Checking for availlable partition

`sudo lvmdiskscan`

![nfs-lvmdiscscan](./images/nfs-lvmdiscscan.PNG)

### Creation of physical volume using pvcreate utility

`sudo pvcreate /dev/xvdf1`

`sudo pvcreate /dev/xvdg1`

`sudo pvcreate /dev/xvdh1`

![nfs-physicalvolume-creation](./images/nfs-physicalvolume-creation.PNG)

### Confirming physical volume availability on NFS server

`sudo pvs`

![nfs-pvs](./images/nfs-pvs.PNG)

### Creation of volume group

`sudo vgcreate webdata-v /dev/xvdh11 /dev/xvdg1 /dev/xvdf1`

![nfs-vggroup-creation](./images/nfs-vggroup-creation.PNG)

### Confirming volume group created

`sudo vgs`

![nfs-sudo-vgs](./images/nfs-sudo-vgs.PNG)

### Creation of logical volumes

`sudo lvcreate -n lv-apps -L 9G webdata-vg`

`sudo lvcreate -n lv-logs -L 9G webdata-vg`

`sudo lvcreate -n lv-opt -L 9G webdata-vg`

![nfs-lvcreate](./images/nfs-lvcreate.PNG)

### Confirming logical volumes created on NFS server using lsblk

`lsblk`

![nfs-lsblk-after-logicalvolume](./images/nfs-lsblk-after-logicalvolume.PNG)

### Confirming the set up so far

`sudo vgdisplay -v #view complete setup - VG, PV and LV`

![nfs-sudo-vgdisplay](./images/nfs-sudo-vgdisplay.PNG)

![nfs-sudo-vgdisplay1](./images/nfs-sudo-vgdisplay1.PNG)

### Disks formating as xfs

`sudo mkfs -t xfs /dev/webdata-vg/lv-apps`

`sudo mkfs -t xfs /dev/webdata-vg/lv-logs`

`sudo mkfs -t xfs /dev/webdata-vg/lv-opt`

![nfs-xfs-format](./images/nfs-xfs-format.PNG)

### Creation of mount

`sudo mkdir /mnt/apps`

`sudo mkdir /mnt/logs`

`sudo mkdir /mnt/opt`

![nfs-mountpoint](./images/nfs-mountpoint.PNG)

### Mounting the created mount

`sudo mount /dev/webdata-vg/lv-apps /mnt/apps`

`sudo mount /dev/webdata-vg/lv-logs /mnt/logs`

`sudo mount /dev/webdata-vg/lv-opt /mnt/opt`

![nfs-mounting](./images/nfs-mounting.PNG)

### Installation of NFS server

`sudo yum update -y`

![nfs-sudo-yum-update-1](./images/nfs-sudo-yum-update-1.PNG)

![nfs-sudo-yum-update-last](./images/nfs-sudo-yum-update-last.PNG)

### NFS utils installation

`sudo yum install nfs-utils`

![nfs-utils](./images/nfs-utils.PNG)

### NFS utils installation

`sudo yum install nfs-utils`

![nfs-utils](./images/nfs-utils.PNG)

![nfs-utils1](./images/nfs-utils1.PNG)

### Starting NFS server services

`sudo systemctl start nfs-server.service`

![nfs-start-nfs-server](./images/nfs-start-nfs-server.PNG)

### Enabling NFS server services

`sudo systemctl enable nfs-server.service`

![nfs-enable-nfs-server](./images/nfs-enable-nfs-server.PNG)

### Confirming the status of NFS server services

`sudo systemctl status nfs-server.service`

![nfs-systemctl-status](./images/nfs-systemctl-status.PNG)

### Setting up permissions that will allow web servers to read, write and execute files on NFS

`sudo chown -R nobody: /mnt/apps`

`sudo chown -R nobody: /mnt/logs`

`sudo chown -R nobody: /mnt/opt`

`sudo chmod -R 777 /mnt/apps`

`sudo chmod -R 777 /mnt/logs`

`sudo chmod -R 777 /mnt/opt`

![nfs-permission-setup](./images/nfs-permission-setup.PNG)

### Restarting NFS server services

`sudo systemctl restart nfs-server.service`

![nfs-systemctl-restart](./images/nfs-systemctl-restart.PNG)

### Confirmin the status again after restarting NFS server services

`sudo systemctl status nfs-server.service`

![nfs-systemctl-status](./images/nfs-systemctl-status.PNG)

### Configuring access to NFS for clients within the same subnet

`sudo vi /etc/exports`

`sudo exportfs -arv`

![nfs-et-exports](./images/nfs-et-exports.PNG)

![nfs-subnet-cidr](./images/nfs-subnet-cidr.PNG)

![nfs-exportfs](./images/nfs-exportfs.PNG)

### Checking which port is being used by NFS and open it using security group.

`rpcinfo -p | grep nfs`

![nfs-check-ports](./images/nfs-check-ports.PNG)

### Security group edit.

![nfs-secgrp](./images/nfs-secgrp.PNG)


## STEP 2 – CONFIGURATION OF DATABASE SERVER


### Installation of MYSQL-server

`sudo apt mysql-server`

![db-mysqlsever-installation-first](./images/db-mysqlsever-installation-first.PNG)

![db-mysqlsever-installation-last](./images/db-mysqlsever-installation-last.PNG)

### Database creation

`sudo mysql`

![db-sudo-mysql](./images/db-sudo-mysql.PNG)

### Editting mysql database configuration file

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

![db-mysql-configuration1](./images/db-mysql-configuration1.PNG)

![db-mysql-configuration](./images/db-mysql-configuration.PNG)

### Restarting and confirming status of mysql

`sudo systemctl restart mysql`

`sudo systemctl status mysql`

![db-mysql-restart-status](./images/db-mysql-restart-status.PNG)

### Database further configuration

![db-showdatases](./images/db-showdatases.PNG)

## STEP 3 – CONFIGURATION OF WEB SERVER


### Installation of NFS client on web server

`sudo yum install nfs-utils nfs4-acl-tools -y`

![web1-install-nfs-utils](./images/web1-install-nfs-utils.PNG)

![web1-install-nfs-utils1](./images/web1-install-nfs-utils1.PNG)

### Mounting /var/www/ and target the NFS server’s export for apps

`sudo mkdir /var/www`

![web1-mkdir](./images/web1-mkdir.PNG)

![web1-nfs-privateipadd](./images/web1-nfs-privateipadd.PNG)


### Verifying NFS was mounted successfully on web server by running df -h

`df -h`

![web1-df-h](./images/web1-df-h.PNG)

### Creating test.md file

`sudo touch /var/www/test.md`

![web1-sudo-touch-testmd](./images/web1-sudo-touch-testmd.PNG)

### Confirming test.md file presence on NFS server

`ls /mnt/apps`

![nfs-testmd](./images/nfs-testmd.PNG)

### Editing /etc/fstab file 

`sudo vi /etc/fstab`

![web1-etc-fstab1](./images/web1-etc-fstab1.PNG)

![web1-etc-fstab](./images/web1-etc-fstab.PNG)

### Installation of Appache on the web server 

`sudo yum install httpd -y`

![web1-install-httpd](./images/web1-install-httpd.PNG)

![web1-install-httpd1](./images/web1-install-httpd1.PNG)

### Verfying Apache on web server

`ls /var/www`

![web1-ls-var-www](./images/web1-ls-var-www.PNG)

### Mounting /var/www/ and target the NFS server’s export for apps

`sudo mkdir /var/www`

![web1-ls-var-www](./images/web1-ls-var-www.PNG)

### GIT installation

`sudo yum install git`

![web1-install-git](./images/web1-install-git.PNG)

![web1-install-git1](./images/web1-install-git1.PNG)

### GIT cloning

`sudo git init`

`git clone https://github.com/darey-io/tooling.git`

![web1-gitinit-gitclone](./images/web1-gitinit-gitclone.PNG)

### Confirming the cloned tooling

`ls`

![web1-ls](./images/web1-ls.PNG)

### cd into tooling

`cd tooling`

![web1-cd-tooling](./images/web1-cd-tooling.PNG)

### Copying tooling files into /var/www/html

`sudo cp -R html/. /var/www/html`

![web1-sudo-cp](./images/web1-sudo-cp.PNG)

### Disabling SELinux

`sudo setenforce 0`

`sudo vi /etc/sysconfig/selinux`

![web1-setenforce1](./images/web1-setenforce1.PNG)


![web1-setenforce](./images/web1-setenforce.PNG)

### Confirming the web page is accessible from anywhere by the end users by displaying apache test page.

![web1-appache-test-page](./images/web1-appache-test-page.PNG)

### Updating the website's configuration to connect to the database (in /var/www/html/functions.php file)

`sudo vi /var/www/html/functions.php`

![web1-functions-php1](./images/web1-functions-php1.PNG)

![web1-functions-php](./images/web1-functions-php.PNG)

### Installation of mysql

`sudo yum install mysql`

![web1-mysql-intallation](./images/web1-mysql-intallation.PNG)

### Applying tooling-db.sql script to the database using this command below

`mysql -h 172.31.1.49 -u webaccess -p tooling < toolingg-db.sql`

![web1-mysql-password](./images/web1-mysql-password.PNG)


### Removing apache test page

`vi /etc/httpd/conf.d/welcome/conf`

`su mv /etc/httpd/conf.d/welcome/conf /etc/httpd/conf.d/welcome.backup`

![web1-vi-welcome-pg1](./images/web1-vi-welcome-pg1.PNG)

![web1-vi-welcome-pg](./images/web1-vi-welcome-pg.PNG)

![web1-welcomepg-backup](./images/web1-welcomepg-backup.PNG)

### Restarting and confirming status of the httpd

`sudo systemctl restart httpd`

![web1-vi-welcome-pg1](./images/web1-httpd-restart.PNG)

![web1-webpg](./images/web1-webpg.PNG)

### Installation of PHP dependencies

`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm`

`sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm`

`sudo yum module list php`

`sudo yum module reset php`

`sudo yum module enable php:remi-7.4`

`sudo yum install php php-opcache php-gd php-curl php-mysqlnd`

`sudo systemctl start php-fpm`

`sudo systemctl enable php-fpm`

`setsebool -P httpd_execmem 1`

![web1-epel9-installation](./images/web1-epel9-installation.PNG)

![web1-utils-installation1](./images/web1-utils-installation1.PNG)

![web1-utils-installation-last](./images/web1-utils-installation-last.PNG)

![web1-phplist-installation1](./images/web1-phplist-installation1.PNG)

![web1-php-reset](./images/web1-php-reset.PNG)

![web1-phpremi1](./images/web1-phpremi1.PNG)

![web1-php-opcache1](./images/web1-php-opcache1.PNG)

![web1-php-opcache-last](./images/web1-php-opcache-last.PNG)

![web1-php-start-enable-status](./images/web1-php-start-enable-status.PNG)

![web1-setsebool](./images/web1-setsebool.PNG)

### Restarting and confirming status of the apache

`sudo systemctl restart httpd`

![web1-vi-welcome-pg1](./images/web1-httpd-restart.PNG)

### After php dependencies installation, refresh the browser and login using login details in the database configuration

`sudo systemctl restart httpd`

![webpage](./images/webpage.PNG)

![admin-page](./images/admin-page.PNG)











