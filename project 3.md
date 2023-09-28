# LAMP Stack implementation - Project 3

### What is a technology stack?

it is a set of frameworks and tools used to develop a software product.

There are various technology stacks that are used which are

**LAMP**  - Linux,Apache,MySQL,PHP/Python/Perl  
**LEMP**  - Linux,Nginx,MySQL,PHP/Python/Perl    
**MERN**   - MongoDB,ExpressJS,Reactjs,AngularJS  
**MEAN**  - MongoDB,ExpressJS,AngularJS,NodeJS

#### In this project i will focus on **LAMP** Technology Stack.


### Step Zero - Preparing Prequsites

1. Register for a new AWS account and launch an EC2 instance.

2. Download and save private key in a secured location - dont share this with anyone

3. connect to AWS EC2 instance using SSH folowing the steps below.

i> change directory to location of downloaded private key using 
`cd <private_key_file_location>`

![change directory](./images/1%20change%20directory%20to%20downloads.PNG)

ii> connect to EC2 instance by typing 

`ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`

![connect to instance](./images/2%20paste%20ssh%20code.PNG)

![instance connected](./images/3%20successfuly%20connected%20to%20aws%20console.PNG)


### Step 1 - Installing Apache and firewall update

#### What is Apache 

Type the following command on the terminal to update the apache server

`sudo apt update`

![install apache](./images/4%20first%20apache%20update.PNG)
 
 to install Apache 2 package type

`sudo apt install apache2`

![apache 2 update](./images/5%20apache%202%20installed.PNG)

to verify apache is running correctly as a service type the below command

`sudo systemctl status apache2`


To access the server locally type

`curl http://localhost:80`

or

`curl http://127.0.0.1:80`

![locally access EC2 running server](<images/6 curl code on apache server.PNG>)


Test the Apache web server using a web broswer using the command below

`http://<Public-IP-Address>:80`

![apache server on a web browser](<images/7 visited my server on the webbrowser.PNG>)

To retrieve our public ip adress without visiting the AWS console use the command below

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

![How to know AWS public ip](<images/8 how to know the ip adress of my aws server.PNG>)


#### Step 2 - Install MySQL 

The next layer of the technology stack is a Database Management system(DBMS) in this case MySQL

to install MySQl on the server type the command below

`$ sudo apt install mysql-server`

![install MySQL](<images/9 install mysql on aws server.PNG>)

to log into MySQL type the command below

`sudo mysql`

![log into mysql](<images/10 log into mysql.PNG>)


To secure Mysql server using a new password type the code below. 

Note the new password to be used is "PassWord.1"

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

![set Mysql password](<images/11 set mysql password.PNG>)


To exit Mysql type the command below

`exit`

![exit mysql](<images/12 exit mysql.PNG>)

To start interactive script type the command below

`$ sudo mysql_secure_installation`

![interractive script 1](<images/13 secure mysql.PNG>)

![interractive script 2](<images/14 follow up.PNG>)


To login to Mysql us the command below

`$ sudo mysql -p`

*the **`-p`** flag gives a prompt so we can type the password we set for root user.

![log into mysql ](<images/15 login to mysql.PNG>)


#### Step 3 - Installing PHP

in this stage there are three component to install which are

**php** - this will process our code to display dynamic content to the end user

**php-mysql** - this is a php module that allows php to communicate with mysql-based databases

**libapache2-mod-php** - this enable apache to handle php files


th install all these three packages at once type the command below

`sudo apt install php libapache2-mod-php php-mysql`


![install all three php packages](<images/16 install php.PNG>)

To determine the php version type the command below

`php -v`

![php version](<images/17 php version.PNG>)


#### Step four - Create a Virtual Host For Website using Apache

A. Create the directory for ***projectlamp*** using the command below

`$ sudo mkdir /var/www/projectlamp`

![create projectlamp directory](<images/21 project lamp directory created.PNG>)

B. Assign ownership of the directory with the   `$user`  environment virable which will refrence my current system user

`$ sudo chown -R $USER:$USER /var/www/projectlamp`

![assign ownership](<images/22 assign ownership.PNG>)

C. Create a new configuration file in apache `sites-available` directory using my prefered command line editor. The vi command line editor will be used

![create configuraion](<images/23 create configuration file.PNG>)

a new blank file will be created afterwards type the following code into the vi text editor

`<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`


![edit configuration](<images/24 edit configuration file.PNG>)

To save the file follow the steps below

a. press the `esc` key

b. type `:`

c. type `wq`  
 **w** for write and **q** for quit

 d. press `enter` to save the file


 use `ls` to show the new files in the `sites-available` directory 

 using the command below

 `$ sudo ls /etc/apache2/sites-available`

You will see something like this

**000-default.conf  default-ssl.conf  projectlamp.conf**

![listed new files in directory](<images/25 new files in directory.PNG>)

use ***a2ensite*** command to enable virtual host typed in the format below

`$ sudo a2ensite projectlamp`

![enable virtual host](<images/26 eneble virtual hosting.PNG>)


if not using a custom domain it is required we disable apache default configuration else apache defult configuration will overwrite our virtual host.

to do so type the command below

`$ sudo a2dissite 000-default`

![disable apache default website](<images/27 disable apache default website.PNG>)

To check for syntax error in the configuration file run the command below

`$ sudo apache2ctl configtest`

![test for syntax error](<images/28 ensure config file does not contain error.PNG>)


Reload Apache for changes to take effect by runnin the command below

`$ sudo systemctl reload apache2`

![reload apache](<images/29 reload apache.PNG>)

The web root **/var/www/projectlamp** is empty hence we create an index.html file in that location so we can test the virtual host works as expected.

to create the index.html file run the code below

`sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`

![create index file to test virtual host](<images/30 create index file to test virtual host.PNG>)

on our web browser open our website url using ec2 public adress using the code

`http://<Public-IP-Address>:80`

#### Step 5 - Enable Php on the website

With the default **Directorylndex** settings on Apache, a file named `inex.html` will always take precedence.
over an `index.php` file. This is useful for setting up maintenance pages in PHP applications, by creating a
temporary `index.html` file containing an informative message to visitors. Because this page wil take
precedence overthe `index.php` page, it will then become the landing page for the application. Once

maintenance s over, the `index. html` is renamed or removed from the document root, bringing back the
regular application page.

In case you want to change this behavior, you'll need to edit the **/etc/apache2/mods-enabled/dir.conf** file and
change the order in which the `index.php` file is listed within the `Directorylndex` directive.

To do so run the command below

`sudo vim /etc/apache2/mods-enabled/dir.conf`

![sudo vim etc apache2](<images/31 sudo vim etc apache2 mods-enabled dir.conf.PNG>)


Make the changes below

<IfModule mod_dir.c>
        # Change this:  

        # DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm  
![before editing](<images/18 enable php on the website.PNG>)


        # To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm

![after editing](<images/19 file name change.PNG>)

</IfModule>

afterwards close and save the file.

After saving the file we reload apache so changes will take effect using the command below.

`$ sudo systemctl reload apache2`

![reload apache](<images/20 reload apache.PNG>)


next we create a new file named `index.php` inside our custom web root folder using the code below.

`$ vim /var/www/projectlamp/index.php`

![new file created](<images/34 new file index php created.PNG>)

after running the above command a blank file will be opened on the vi editor paste the PHP code below which is a valid code inside the file.

`<?php
phpinfo();`

![valid php code](<images/35 php valid code.PNG>)

after saving the above code we refresh our webpage to see the result below

![display php browser result](<images/36 pdp browser display page.PNG>)


to remove file creeated we run the code below using `rm`

`$ sudo rm /var/www/projectlamp/index.php`

![remove relevant information](<images/36 remove relevant information to avoid risk exposure.PNG>)