# 01.LAMPSTACK
## **/1.Deploying_My_LAMP_STACK_APPLICATION/**

**A LAMP** stack is a bundle of four different software technologies that developers use to build websites and web applications. LAMP is an acronym for the operating system, Linux; the web server, Apache; the database server, MySQL; and the programming language, PHP.

**Linux:**
Linux is an open-source operating system that you can install and configure to meet different application requirements. Linux sits at the first level of the LAMP stack and supports other components on the upper layers. It is used in deploying other components.

**APACHE:**
This a web server that forms the second layer of the LAMP stack. It stores website files and exchanges information with a browser using HTTP, It receives request, processes the request and finds the required page file and Sends the relevant information back to the browser.

**MySQL:**
A relational database management system and is the third layer of the LAMP stack. The LAMP model uses MySQL for storing, managing, and querying information in relational databases.Query refers to special instructions for manipulating data in a relational database with the SQL language.

**PHP:**
It is a scripting language that allows websites to run the elements of LAMP stack dynamically.


# **CREATION OF EC2 INSTANCE**

First step: Log on to the AWS Cloud Services and Create an EC2 Ubuntu Virtual Machine(VM) instance. When creating the instance, choose keypair aunthentication and download private key (*.pem) on your local computer.

![ec2](https://user-images.githubusercontent.com/77943759/216826242-67b946f0-8297-4e0b-bb85-5caf9e95c4c9.png)


# **CONFIGURING SECURITY GROUP INBOUND RULES ON EC2 INSTANCE**
A security group is a group of rules that acts as a virtual firewall to the type of traffic that enters or leaves an instance-inbound and outbound traffics respectively.

We need to enable our inbound traffic from anywhere in order to ensure our webpage accessibility on the internet.

![image](https://user-images.githubusercontent.com/77943759/216844372-df747561-8f6c-4eef-a1f1-b72953b1895c.png)


We can check the web server accessibility on the internet with curl alongside the IP address or DNS name of our local host.

`curl http://localhost:80 or curl http://127.0.0.1:80`

# **APACHE WEB SERVER SETUP**
We install apache through Ubuntu package manager `apt` in order to deploy the web application
```
  #updating Packages
  
  $ sudo apt update
  
  $ sudo apt install apache2
```
![apache2install](https://user-images.githubusercontent.com/77943759/216845314-2079e080-4c0d-4672-80c6-1ab814e91060.png)

```
#starting apache2 Server
$ systemctl start apache2

#ensuring apache2 strts automatically when system boots
$ systemctl enable apache2

#checking server
$ systemctl status apache2
```
![apachestatus](https://user-images.githubusercontent.com/77943759/216845944-9e6ff118-359e-49a2-8743-c3019a67eb01.png)

If is shows this green text, the web server is up and running

# **INSTALLING MySQL**
MySQL is a relational database which we use to store and manage data on our site
Use `sudo apt install mysql` command to install it

![mysql](https://user-images.githubusercontent.com/77943759/216848898-d7fd1b7e-9164-4ad4-96aa-6d2e9b7c1243.png)

Use `sudo mysql_secure_installation` command remove unwanted default permissions and enable database protection

![mysqlenable](https://user-images.githubusercontent.com/77943759/216849193-22bd24b8-7018-450e-95e4-873afb1442e6.png)

To access the mysql Database, enter this on the terminal
`sudo mysql` and simple type `exit` to leave the MYSQL terminal


# **PHP AND ITS MODULES INSTALLATION**
PHP serves as the programming language responsible for dynamically displaying contents of the webpage to users who make requestbto the server
 In addition to the php package, we will need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. We also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies. Run this code to install them. 
 
 `sudo apt install php libapache2-mod-php php-mysql`
 
 ![php](https://user-images.githubusercontent.com/77943759/216851262-51952271-8333-4c47-89ad-6be47148229a.png)
 
 Once the installation is finished, you can run the following command to confirm your PHP version:

`php -v`

![phpv](https://user-images.githubusercontent.com/77943759/216851476-9df9ebbc-b8b3-42e0-9979-369bd46c5201.png)

# **CREATING VIRTUAL HOST FOR OUR WEBSITE USING APACHE**

Apache VirtualHost ensures that one or more websites can run on webserver via different ip addresses. We set up Virtual host to enable us display our webcontents on web server

# **Creating web Domain**

First, we create the directory for projectlamp using the mkdir command
`sudo mkdir /var/www/projectlampstack`

Next, we assign ownership of the directory with the current system user:

 `sudo chown -R $USER:$USER /var/www/projectlamp`
 
 ![Screenshot from 2023-02-05 23-38-17](https://user-images.githubusercontent.com/77943759/216852500-c1319e10-d4f1-481c-922d-fc76c3902e6e.png)

 
 Then, create and open a new configuration file in Apache’s sites-available directory

`sudo vi /etc/apache2/sites-available/projectlamp.conf`

copy and paste the following in he directory, it is the setting required to spin the server block
```
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
![Screenshot from 2023-02-05 23-40-54](https://user-images.githubusercontent.com/77943759/216852786-5790e68c-ac94-4edc-8cb8-2b70e3d80331.png)

Ater this, hit `esc` then type :wq to save and exit the vi editor

Next, run `sudo a2ensite projectlampstack` to activate the server block

You need to deactivate the default server block that is defaultly on apache:

`sudo a2dissite 000-default`

Now reload the apache2 server:

`sudo systemctl reload apache2`

To test that the server is working as expected, we create an index.html file in /var/www/projectlampstack

![Screenshot from 2023-02-05 23-58-12](https://user-images.githubusercontent.com/77943759/216853477-93a49201-e706-45b8-a71f-d52ad285893e.png)

Now, open any browser of your choice and type in 

`http://<public_ip_address>:80`

![Screenshot from 2023-02-06 00-05-00](https://user-images.githubusercontent.com/77943759/216854171-f57a2d5a-5581-42a7-af51-04e95a170ebd.png)

# **ENABLE PHP ON WEBSITE**

By default, index.html will take preceedence over index.php, to change this behaviour, open the editor

`sudo vim /etc/apache2/mods-enabled/dir.conf`

Then, paste this command

```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

![Screenshot from 2023-02-06 01-02-45](https://user-images.githubusercontent.com/77943759/216858925-029ae13c-0ad2-4d74-9b13-c4aa97e41b9c.png)


Reload apache for changes to take effect

`sudo systemctl reload apache2`

Create an index.php file

`vim /var/www/projectlamp/index.php`

Input this:
```
<?php
phpinfo();
```

![Screenshot from 2023-02-06 01-09-33](https://user-images.githubusercontent.com/77943759/216859517-1d2afed3-66d8-4560-a253-29b073847303.png)

Refresh your opened page or repeat 

`http://<public_ip_address>:80`


![Screenshot from 2023-02-06 01-11-29](https://user-images.githubusercontent.com/77943759/216859664-d47bcbab-979a-4bc9-837e-3e45e4e2d33a.png)

End
