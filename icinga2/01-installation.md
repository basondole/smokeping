# Installation of Icinga 2 on CentoS

## Install required packages
<pre>
[basondole@localhost ~]
sudo yum install httpd
sudo yum install mariadb mariadb-server
sudo systemctl start mariadb && sudo systemctl enable mariadb
sudo mysql_secure_installation
sudo yum install centos-release-scl
sudo yum install rh-php71-php-mysqlnd rh-php71-php-cli php-Icinga rh-php71-php-common rh-php71-php-fpm rh-php71-php-pgsql rh-php71-php-ldap rh-php71-php-intl rh-php71-php-xml rh-php71-php-gd rh-php71-php-pdo rh-php71-php-mbstring -y
</pre>

Verify the timezone is properly configured
<pre>
[basondole@localhost ~]
cat /etc/opt/rh/rh-php71/php.ini | grep timezone
; Defines the default timezone used by the date functions
; http://php.net/date.timezone
date.timezone = Africa/Nairobi
</pre>

if date.timezone is empty edit the file to add correct timezone

### Install more dependencies
<pre>
[basondole@localhost ~]
sudo yum -y install http://mirror.centos.org/centos/7/sclo/x86_64/sclo/sclo-php71/sclo-php71-php-pecl-imagick-3.4.3-2.el7.x86_64.rpm
sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
sudo yum install https://packages.icinga.com/epel/icinga-rpm-release-7-latest.noarch.rpm
sudo yum install icinga2-ido-mysql icingaweb2 icingacli nagios-plugins-all
</pre>

## Install icinga
<pre>
[basondole@localhost ~]
yum install icinga2
</pre>

Start and enable the services
<pre>
[basondole@localhost ~]
sudo systemctl start httpd.service
sudo systemctl start icinga2.service
sudo systemctl start rh-php71-php-fpm.service
sudo systemctl enable rh-php71-php-fpm.service
sudo systemctl enable icinga2.service
</pre>


## Configuring the firewall
<pre>
[basondole@localhost ~]
sudo firewall-cmd --zone=public --permanent --add-port=80/tcp 
sudo firewall-cmd --zone=public --permanent --add-port=5665/tcp
sudo firewall-cmd --reload
</pre>

Now icinga2 is successfully installed and can be accessed on a browser to finalize the setup proccess  
via `http://<ip-addrees-or-hostname-of-icnga2-server>/icingaweb2/setup`  


## Configuring icinga2 web
My installation is done on a vm with IP 192.168.56.4  
I will browse `http://192.168.56.4/icingaweb2/setup`  

![icinga1](https://user-images.githubusercontent.com/50369643/68597564-3c41fe80-04ae-11ea-9229-8e2268917047.png)

### Create token:
<pre>
[basondole@localhost ~]
sudo groupadd -r icingaweb2
sudo usermod -a -G icingaweb2 apache
sudo icingacli setup config directory --group icingaweb2
sudo icingacli setup token create
</pre>

Fill the token to the web interface and click next

![icinga2](https://user-images.githubusercontent.com/50369643/68597567-3cda9500-04ae-11ea-903b-13cd326e1b3c.png)  

![icinga3](https://user-images.githubusercontent.com/50369643/68597544-3815e100-04ae-11ea-8965-9517e170ff8c.png)  

Review the modules and click next  

![icinga4](https://user-images.githubusercontent.com/50369643/68597545-38ae7780-04ae-11ea-8fe6-8d9a3f2dbc25.png)  

### Create Icinga database
<pre>
[basondole@localhost ~]
mysql -u root -p
Enter password:
MariaDB [(none)]> create database icinga2;
MariaDB [(none)]> exit
</pre>

Fill in the details as illustrated and click validate configuration then next  

![icinga5](https://user-images.githubusercontent.com/50369643/68597547-38ae7780-04ae-11ea-8486-86eaec2901ae.png)

![icinga6](https://user-images.githubusercontent.com/50369643/68597548-38ae7780-04ae-11ea-936f-bfde35cf9f43.png)  

![icinga7](https://user-images.githubusercontent.com/50369643/68597549-39470e00-04ae-11ea-99e0-ac6054c56b94.png)  

![icinga8](https://user-images.githubusercontent.com/50369643/68597550-39470e00-04ae-11ea-9dc0-a4bde81f5200.png)  


On the next screen, we will be asked to review the changes that we have made for icinga.  
Make sure that everything is in order & hit next  

![icinga9](https://user-images.githubusercontent.com/50369643/68597551-39dfa480-04ae-11ea-977a-82b3ee5169dc.png)  

![icinga10](https://user-images.githubusercontent.com/50369643/68597552-39dfa480-04ae-11ea-829f-10a139a53ce1.png)  


## Configuring monitoring module for Icinga
<pre>
[basondole@localhost ~]
mysql -u root -p 
 MariaDB [(none)]> CREATE DATABASE icinga; 
 MariaDB [(none)]> GRANT SELECT, INSERT, UPDATE, DELETE, DROP, CREATE VIEW, INDEX, EXECUTE ON icinga.* TO 'baggy'@'localhost' IDENTIFIED BY 'password'; 
 MariaDB [(none)]> FLUSH PRIVILEGES; 
 MariaDB [(none)]> EXIT;
mysql -u root -p icinga < /usr/share/icinga2-ido-mysql/schema/mysql.sql
</pre>

### Configure IDO
<pre>
[basondole@localhost ~]
sudo cat /etc/icinga2/features-available/ido-mysql.conf
/**
 * The IdoMysqlConnection type implements MySQL support
 * for DB IDO.
 */
object IdoMysqlConnection "ido-mysql" {
  //user = "icinga"
  //password = "icinga"
  //host = "localhost"
  //database = "icinga"
}
</pre>

Edit the above file to become
<pre>
[basondole@localhost ~]
sudo cat /etc/icinga2/features-available/ido-mysql.conf
/**
 * The IdoMysqlConnection type implements MySQL support
 * for DB IDO.
 */
library "db_ido_mysql"
object IdoMysqlConnection "ido-mysql" {
  user = "icinga"
  password = "icinga"
  host = "localhost"
  database = "icinga"
}
</pre>

Check if IDO is enable 
<pre>
[basondole@localhost ~]
sudo icinga2 feature list
</pre>

if not enabled, enable with
<pre>
[basondole@localhost ~]
sudo icinga2 feature enable ido-mysql
sudo icinga2 feature enable command
sudo systemctl restart icinga2
</pre>

![icinga11](https://user-images.githubusercontent.com/50369643/68597560-3ba96800-04ae-11ea-9367-6e6a6f39f707.png)  

If you get below error upon validation
> "There is currently no icinga instance writing to the IDO. Make sure that a icinga instance is configured and able to write to the IDO.”

Issue these commands  

<pre>
[basondole@localhost ~]
sudo chmod 660 /etc/icinga2/features-available/ido-mysql.conf
sudo chown icinga:icinga /etc/icinga2/features-available/ido-mysql.conf
</pre>

![icinga12](https://user-images.githubusercontent.com/50369643/68597554-39dfa480-04ae-11ea-9367-9656e4696f5f.png)  

![icinga13](https://user-images.githubusercontent.com/50369643/68597556-3a783b00-04ae-11ea-9ad7-7217f722b875.png)  

## Login to icinga2

![icinga14](https://user-images.githubusercontent.com/50369643/68597557-3b10d180-04ae-11ea-8a4d-db5eed8f8e04.png)  

![icinga15](https://user-images.githubusercontent.com/50369643/68597559-3b10d180-04ae-11ea-9dce-82aa937fed47.png)  

### Adding Nodes to be monitored
<pre>
[basondole@localhost ~]
nano /etc/icinga2/conf.d/hosts.conf
object Host “router-63” {
  address = “192.168.56.63”
  check_command = “hostalive”
}
</pre>

Restart the service
<pre>
[basondole@localhost ~]
sudo systemctl restart icinga2.service
</pre>

On the web page navigate to hosts

![icinga16](https://user-images.githubusercontent.com/50369643/68597561-3ba96800-04ae-11ea-8ca1-8fef800a9abc.png)

