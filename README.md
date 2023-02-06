# Project-02
## :ledger: Index

- [About The Project](#beginner-about-the-project)
- [Problem that this project solves ](#question-problem-that-this-project-solves)
- [Solution to the problem.](#key-solution-to-the-problem)
- [Tools](#hammer_and_wrench-Tools)
- [Architecture of this project](#house-architecture-of-this-project)
- [Steps to Execute the project](#zap-steps-to-execute-the-project)
  - [Setup tools ](#electric_plug-setup-tools )
  - [Clone source code](#package-slone-source-code)
  - [Go to the vagrant dir](#package-go-to-the-vagrant-dir)
  - [Bring up the Virtual machines ](#bulb-bring-up-the-virtual-machines)a
  - [Validate](#heavy_check_mark-validate)
  - [Setup All the services](#package-setup-all-the-services)
    - [Mysql](#package-Mysql)
    - [Memcached](#package-Memcached)
    - [Rabbit MQ](#package-Rabbit-MQ)
    - [Tomcat](#package-Tomcat)
    - [Nginx](#package-Nginx)
    - [Mysql](#package-Mysql)
- [App Build](#hammer-build)  
- [App Deployment](#rocket-deployment) 
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

![Project Image](project-image-url)

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>

## Steps to Execute the project. 

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
 f/vprofile-project/vagrant/Automated_provisioning (local-setup)
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

### Create bash script for memcache

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


### :bulb: Bring up the Virtual machines 

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


### :heavy_check_mark:Validate

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


### Setup All the services
1. **Mysql** <br>
2. **Memcached** <br>
3. **Rabbit MQ** <br>
4. **Tomcat** <br>
5. **Nginx** <br>

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>



## App Deployment

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## :earth_africa: Verify from browser


<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## :page_facing_up: Resources

<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>


## :star2: Acknowledgment


<br/>
<div align="right">
    <b><a href="#project-02">↥ back to top</a></b>
</div>
<br/>

