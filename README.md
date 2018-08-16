# Linux-Server-Configuration

#Acccess

ssh grader@18.220.128.196 -p 2200 -i ~/.ssh/id_rsa

http://18.220.128.196

18.220.128.196

http://ec2-18-220-128-196.us-east-2.compute.amazonaws.com


ec2-18-220.128.196.us-east-2.compute.amazonaws.com

#Reference

https://lightsail.aws.amazon.com/ls/webapp/us-east-2/instances/Ubuntu-Please/snapshot?#

https://console.developers.google.com/apis/credentials?project=restaurant-menu-app-196801

https://github.com/twhetzel/item-catalog

https://github.com/harushimo/linux-server-configuration

#Steps to configuration

#Lightsail SSH login

sudo adduser grader

#Password and name added

#Check addition

sudo cat /etc/passwd

#grader should be listed

#Give Grader sudo permissions

sudo visudo

# below ROOT add 

grader  ALL=(ALL:ALL) ALL

#Also from grader home

sudo nano /etc/sudoers.d/grader

#enter

grader  ALL=(ALL) NOPASSWD:ALL

#Key-based Auth/Public Key

#local machine

ssh-keygen

#Two keys  .pub must be placed on server
  from grader home 

mkdir .ssh

touch .ssh/authorized_keys 

sudo  nano .ssh/authorized_keys

#paste .pub key into authorized_keys  file

#set permissions on .ssh files as grader

chmod 700 .ssh

chmod 664 .ssh/authorized_keys

#disable password login force keypair

sudo nano /etc/ssh/sshd_config

Port 2200

PermitRootLogin no

PasswordAuthentication no

#save

sudo service ssh restart

Snapshot SSH Foundation

# Firewall Settings

sudo  ufw default deny incoming

sudo  ufw default allow outgoing

sudo  ufw allow ssh

sudo  ufw allow 2200/tcp

sudo  ufw allow www

sudo  ufw allow 123/udp

sudo  ufw enable

sudo ufw status

To                   Action      From
--                   ------      ----
22                   ALLOW       Anywhere
2200/tcp             ALLOW       Anywhere
80/tcp               ALLOW       Anywhere
123/udp              ALLOW       Anywhere
22 (v6)              ALLOW       Anywhere (v6)
2200/tcp (v6)        ALLOW       Anywhere (v6)
80/tcp (v6)          ALLOW       Anywhere (v6)
123/udp (v6)         ALLOW       Anywhere (v6)

#Port 22 will need to be disabled prior to submission

#ensure you can still ssh into the server

#Configure timezone to UTC

sudo dpkg-reconfigure tzdata

#highlight None of the above ENTER

#highlight UTC ENTER

Snapshot FW UTC SET

#check for and install upgrades

sudo apt-get update

sudo apt-get upgrade

#install and configure Apache to serve Python
  mod_wsgi application

sudo apt-get install apache2

# http:your.aws.ip  should return an about apache2
  page so you know the install is succesful

Snapshot Upgrades Apache2

#Install WSGIapp

sudo apt-get install libapache2-mod-wsgi

#Install & configure PostgreSQL

sudo apt-get install postgresql postgresql-contrib

# Server Setup

sudo -u postgres psql postgres

\password postgres

Snapshot WSGI POSTGRESQL

#New DB/USER/Permissions

sudo su - postgres

#postgresql prompt

psql

#new user

CREATE USER catalog WITH PASSWORD 'your_passwd';

#confirm user created

\du

ALTER ROLE catalog WITH LOGIN;

ALTER USER catalog CREATEDB;

#create the DB

CREATE DATABASE catalog WITH OWNER catalog;

#login DB

\c catalog

REVOKE ALL ON SCHEMA public FROM public;

GRANT ALL ON SCHEMA public TO catalog;

\q  then exit to escape

sudo service postgresql restart

#install and configure GIT

sudo apt-get install git

git config --global user.name "Your Name"

git config --global user.email youremail@domain.com

git config --list

Snapshot DB GIT 

sudo git clone https://github.com/tortorib/item-catalog.git CatalogProject

# at the root of the web directory add .htaccess file 

sudo  mkdir .htaccess

sudo nano /.htaccess

#put  text below in file

RedirectMatch 404 /\.git

#Install Flask and create Virtual Environment

sudo apt-get install python-pip

sudo pip install virtualenv

sudo virtualenv venv

#install Flask in that environment

source venv/bin/activate

sudo pip install Flask


https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

##Examples:

#WSGI

#!/usr/bin/python
import sys
import logging

logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/catalogproject/")

from __init__ import app as application
application.secret_key = 'super_secret_key'


# Catalogproject.conf

<VirtualHost *:80>
                ServerName 18.222.117.226
                ServerAlias ec2-18-222-117-226.us-east-2.compute.amazonaws.com
                ServerAdmin tortorid@server.net
                WSGIScriptAlias / /var/www/catalog/catalogproject/catalogproject.wsgi
                <Directory /var/www/catalog/catalogproject/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalog/catalogproject/static
                <Directory /var/www/catalog/catalogproject/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>



#/etc/hosts

127.0.0.1 localhost
#ec2-18-222-117-226.us-east-2.compute.amazonaws.com
18.2220.128.196

# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

ff02::3 ip6-allhosts
