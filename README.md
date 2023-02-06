# Project-02
## :ledger: Index

- [About The Project](#beginner-about-the-project)
- [Problem that this project solves ](#question-problem-that-this-project-solves)
- [Solution to the problem.](#key-solution-to-the-problem)
- [Tools](#hammer_and_wrench-Tools)
- [Architecture of this project](#house-architecture-of-this-project)
- [Steps to execute the project](#zap-steps-to-execute-the-project)
  - [Clone source code](#package-slone-source-code)
  - [Create bash script for DB](#package-create-bash-script-for-db)
  - [Create bash script for Memcache](#package-create-bash-script-for-memcache)
  - [Create bash script for RabbitMQ](#package-create-bash-script-for-rabbitmq)
  - [Create bash script for Tomcat](#package-create-bash-script-for-tomcat)
  - [Create application properties file](#package-create-application-properties-file)
  - [Create the vagrant file to provision the VMs](#package-create-the-vagrant-file-to-provision-the-vms)
  - [Bring up the Virtual machines ](#bulb-bring-up-the-virtual-machines)a
  - [Validate](#heavy_check_mark-validate)
- [Verify from browser](#earth_africa-verify-from-browser) 
- [Resources](#page_facing_up-resources)
- [Acknowledgment](#star2-acknowledgment)


## :beginner:About The Project
- This is a follow up project from the [local app setup](https://github.com/sheygildas/Local_App_Setup) project
- This is a multi tier web application stack.
- This project can be setup locally on a laptop/desktop

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## :question: Problem that this project solves 

- You may not be comfortable making changes real-world systems.
- Setting up applications manually on a local machine is time consuming and not repeatable.

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## :key: Solution to the problem.

- This project is automated.
- This project is also repeatable.
- Also, this is an infrastructure as a code setup locally.

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>

## :hammer_and_wrench: Tools

- Oracle VM Virtualbox
- Git bash
- visual studio code
- Vagrant

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## :house: Architecture of this project.

![architecture](https://github.com/sheygildas/Automate_Local_App_Setup-/blob/main/images/architecture.png)

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>

## Steps to execute the project. 

### Write the Bash Scripts for the Vitual Macchines

- We will write the scripts to provision our VMs. You can check out the script for this project on the github repository given below.

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>

### Clone source code

- clone the repository from github through the gitbash 

```sh
 git clone https://github.com/sheygildas/Local_App_Setup.git
   ```
   
<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>

### Change to the Automated_provisioning dir

- Change to the Automated_provisioning directory source code through the gitbash. Mine is at;

```sh
 f/Local_App_Setup/vagrant/Automated_provisioning(local-setup)
   ```
   
<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


### Create bash script for the DB

- Create a script for mysql database with the tittle *mysql.sh*. The content  of the script is shown below.


```sh
#!/bin/bash
DATABASE_PASS='admin123'
sudo yum update -y
sudo yum install epel-release -y
sudo yum install git zip unzip -y
sudo yum install mariadb-server -y
# starting & enabling mariadb-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
cd /tmp/
git clone -b local-setup https://github.com/devopshydclub/vprofile-project.git
#restore the dump file for the application
sudo mysqladmin -u root password "$DATABASE_PASS"
sudo mysql -u root -p"$DATABASE_PASS" -e "UPDATE mysql.user SET Password=PASSWORD('$DATABASE_PASS') WHERE User='root'"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1')"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.user WHERE User=''"
sudo mysql -u root -p"$DATABASE_PASS" -e "DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%'"
sudo mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
sudo mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
sudo mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'localhost' identified by 'admin123'"
sudo mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'%' identified by 'admin123'"
sudo mysql -u root -p"$DATABASE_PASS" accounts < /tmp/vprofile-project/src/main/resources/db_backup.sql
sudo mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
# Restart mariadb-server
sudo systemctl restart mariadb

#starting the firewall and allowing the mariadb to access from port no. 3306
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl restart mariadbBash Script for Memcached
   ```

### Create bash script for Memcache

- Create a script for memcached with the tittle *memcache.sh*. The content  of the script is shown below.

```sh
 #!/bin/bash
sudo yum install epel-release -y
sudo yum install memcached -y
sudo systemctl start memcached
sudo systemctl enable memcached
sudo systemctl status memcached
sudo memcached -p 11211 -U 11111 -u memcached -d
   ```

### Create bash script for RabbitMQ

- Create a script for  RabbitMQ with the tittle *rabbitmq.sh*. The content  of the script is shown below.

```sh
#!/bin/bash
sudo yum install epel-release -y
sudo yum update -y
sudo yum install wget -y
cd /tmp/
wget http://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
sudo rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
sudo yum -y install erlang socat
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
sudo yum install rabbitmq-server -y
sudo systemctl start rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl status rabbitmq-server
sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
sudo systemctl restart rabbitmq-server
   ```

### Create bash script for Tomcat

- Create a script for tomcat with the tittle *tomcat.sh*. The content  of the script is shown below.

```sh
 TOMURL="https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.gz"
yum install java-1.8.0-openjdk -y
yum install git maven wget -y
cd /tmp/
wget $TOMURL -O tomcatbin.tar.gz
EXTOUT=`tar xzvf tomcatbin.tar.gz`
TOMDIR=`echo $EXTOUT | cut -d '/' -f1`
useradd --shell /sbin/nologin tomcat
rsync -avzh /tmp/$TOMDIR/ /usr/local/tomcat8/
chown -R tomcat.tomcat /usr/local/tomcat8

rm -rf /etc/systemd/system/tomcat.service

cat <<EOT>> /etc/systemd/system/tomcat.service
[Unit]
Description=Tomcat
After=network.target
[Service]
User=tomcat
Group=tomcat
WorkingDirectory=/usr/local/tomcat8
#Environment=JRE_HOME=/usr/lib/jvm/jre
Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_PID=/var/tomcat/%i/run/tomcat.pid
Environment=CATALINA_HOME=/usr/local/tomcat8
Environment=CATALINE_BASE=/usr/local/tomcat8
ExecStart=/usr/local/tomcat8/bin/catalina.sh run
ExecStop=/usr/local/tomcat8/bin/shutdown.sh
RestartSec=10
Restart=always
[Install]
WantedBy=multi-user.target
EOT
systemctl daemon-reload
systemctl start tomcat
systemctl enable tomcat
git clone -b local-setup https://github.com/devopshydclub/vprofile-project.git
cd vprofile-project
mvn install
systemctl stop tomcat
sleep 60
rm -rf /usr/local/tomcat8/webapps/ROOT*
cp target/vprofile-v2.war /usr/local/tomcat8/webapps/ROOT.war
systemctl start tomcat
sleep 120
cp /vagrant/application.properties /usr/local/tomcat8/webapps/ROOT/WEB-INF/classes/application.properties
systemctl restart tomcat

   ```
   
### Create bash script for Nginx

- Create a script for nginx with the tittle *nginx.sh*. The content  of the script is shown below.

```sh
# adding repository and installing nginx		
apt update
apt install nginx -y
cat <<EOT > vproapp
upstream vproapp {
 server app01:8080;
}
server {
  listen 80;
location / {
  proxy_pass http://vproapp;
}
}
EOT

mv vproapp /etc/nginx/sites-available/vproapp
rm -rf /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp

#starting nginx service and firewall
systemctl start nginx
systemctl enable nginx
systemctl restart nginx

   ```
   
   
### Create application properties file

- Create application properties file with the tittle *application.propertiesh*. The content  of the script is shown below.

```sh
#JDBC Configutation for Database Connection
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://db01:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
jdbc.username=admin
jdbc.password=admin123

#Memcached Configuration For Active and StandBy Host
#For Active Host
memcached.active.host=mc01
memcached.active.port=11211
#For StandBy Host
memcached.standBy.host=127.0.0.2
memcached.standBy.port=11211

#RabbitMq Configuration
rabbitmq.address=rmq01
rabbitmq.port=5672
rabbitmq.username=test
rabbitmq.password=test

#Elasticesearch Configuration
elasticsearch.host =192.168.1.85
elasticsearch.port =9300
elasticsearch.cluster=vprofile
elasticsearch.node=vprofilenode
   ```
- Note: If you made some changes to your backend configuration, make sure you update it in the application.properties file.
 
### Create the Vagrant file to provision the VMs

- Create a Vagrant file with the tittle *Vagrantfile*. The content  of the script is shown below.

```sh
 Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true 
  config.hostmanager.manage_host = true
  
### DB vm  ####
  config.vm.define "db01" do |db01|
    db01.vm.box = "geerlingguy/centos7"
    db01.vm.hostname = "db01"
    db01.vm.network "private_network", ip: "192.168.56.15"
    db01.vm.provision "shell", path: "mysql.sh"  

  end
  
### Memcache vm  #### 
  config.vm.define "mc01" do |mc01|
    mc01.vm.box = "geerlingguy/centos7"
    mc01.vm.hostname = "mc01"
    mc01.vm.network "private_network", ip: "192.168.56.14"
    mc01.vm.provision "shell", path: "memcache.sh"  
  end
  
### RabbitMQ vm  ####
  config.vm.define "rmq01" do |rmq01|
    rmq01.vm.box = "geerlingguy/centos7"
  rmq01.vm.hostname = "rmq01"
    rmq01.vm.network "private_network", ip: "192.168.56.16"
    rmq01.vm.provision "shell", path: "rabbitmq.sh"  
  end
  
### tomcat vm ###
   config.vm.define "app01" do |app01|
    app01.vm.box = "geerlingguy/centos7"
    app01.vm.hostname = "app01"
    app01.vm.network "private_network", ip: "192.168.56.12"
    app01.vm.provision "shell", path: "tomcat.sh"  
    app01.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
   end
   end
   
  
### Nginx VM ###
  config.vm.define "web01" do |web01|
    web01.vm.box = "ubuntu/xenial64"
    web01.vm.hostname = "web01"
  web01.vm.network "private_network", ip: "192.168.56.11"
  web01.vm.provision "shell", path: "nginx.sh"  
end
  
end
   ```
### :bulb: Bring up the Virtual machines 

- Now, go to the repository that we cloned ealier and cd to the directory that our Vagrantfile is found.Mine is at;

```sh
 f/Local_App_Setup/vagrant/Automated_provisioning (local-setup)
   ```
-  RUN the command below to setup the VMs.
*NOTE*:This will also bootstrap our servers with the meta data from our script and provision out App automatically.

```sh
 vagrant up
   ```

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


### :heavy_check_mark: Validate

- validate the VMs one after the other with command vagrant ssh *<name_of_VM_given_in_Vagrantfile>* 

 ```sh
vagrant ssh web01
   ```
- checke */etc/hosts* file of web01 using the command below, the */etc/hosts* file will be updated automatically.
 ```sh
cat /etc/hosts
   ```
- Now, try to ping *app01* from *web01* by running the command below.
 ```sh
ping app01
   ```
   
![ping](https://github.com/sheygildas/Local_App_Setup/blob/local-setup/images/ping.jpg))

- If you were able to connect to *app01* successfully then type *logout* on the terminal and try to ping other services similarly.
- Lets connect to *app01* vbox and check connectivity of *app01* with *rmq01*, *db01* and *mc01*
 ```sh
cat /etc/hosts
ping rmq01
ping db01
ping mc01
logout
   ```

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## :earth_africa: Verify from browser

- Login into *web01* (nginx) server, and run *ifconfig* to get its IP address. My case, the IP address of our web01 is : 192.168.56.11.

```sh
ifconfig
   ``` 
   
- Validate whether Nginx is running by entering *http://<IP_of_Nginx_server>* on your browser .


![Nginx](https://github.com/sheygildas/Local_App_Setup/blob/local-setup/images/Nginx.jpg))

- Validate Db connection using credentials by entering *admin_vp* for both username and password on the login page.

![Db%20connection](https://github.com/sheygildas/Local_App_Setup/blob/local-setup/images/Db%20connection.jpg))

- On the page, Click on RabbitMQ to validate the connection.

![Rabbit%20connection](https://github.com/sheygildas/Local_App_Setup/blob/local-setup/images/Rabbit%20connection.jpg))

- Validate Memcache connection by clicking MemCache.

![Memcache%20connection](https://github.com/sheygildas/Local_App_Setup/blob/local-setup/images/Memcache%20connection.jpg))

- Click on the user ID AND validate if data is coming from Database.

![Memcache%20connection%202](https://github.com/sheygildas/Local_App_Setup/blob/local-setup/images/Memcache%20connection%202.jpg)) 

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## CleanUp

- On the terminal, move to the directory carrying our vagrant file, and run the command below to destroy all virtual machines.


```sh
vagrant destroy
   ``` 
 ![clean](https://github.com/sheygildas/Local_App_Setup/blob/local-setup/images/clean.png))   
   
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>


## :page_facing_up: Resources

* [Check out  the source of this  project on Udemy](https://www.udemy.com/course/devopsprojects/)

<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## :star2: Acknowledgment

Wish to thank Imran Teli for providing such a great [content](https://www.udemy.com/course/devopsprojects/)

<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

