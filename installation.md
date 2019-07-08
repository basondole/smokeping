# Installation for CentOS 7
**[baggy@localhost ~]$** sudo yum install epel-release -y  
**[baggy@localhost ~]$** sudo yum install mod_fcgid httpd httpd-devel rrdtool perl-CGI-SpeedyCGI fping rrdtool-perl perl perl-Sys-Syslog -y  
**[baggy@localhost ~]$** sudo yum install perl-CPAN perl-local-lib perl-Time-HiRes –y  
**[baggy@localhost ~]$** sudo yum groupinstall "Development tools" –y  

**[baggy@localhost ~]$** sudo wget http\://oss\.oetiker\.ch/smokeping/pub/smokeping-2.6.11.tar.gz  
**[baggy@localhost ~]$** tar -zxvf smokeping-2.6.11.tar.gz  
**[baggy@localhost ~]$** cd smokeping-2.6.11/setup  

**[baggy@localhost setup]$** sudo ./build-perl-modules.sh  
**[baggy@localhost setup]$** sudo mkdir /opt/smokeping  
**[baggy@localhost setup]$** cd ..  

**[baggy@localhost smokeping-2.6.11]$** sudo cp -r thirdparty /opt/smokeping/  
**[baggy@localhost smokeping-2.6.11]$** ./configure --prefix=/opt/smokeping  
**[baggy@localhost smokeping-2.6.11]$** sudo /usr/bin/gmake install  
**[baggy@localhost smokeping-2.6.11]$** cd /opt/smokeping/etc/  

**[baggy@localhost etc]$** sudo cp /opt/smokeping/etc/basepage.html.dist /opt/smokeping/etc/basepage.html  
**[baggy@localhost etc]$** for FOO in \*.dist; do sudo cp \$FOO \`basename \$FOO .dist\`\;done  
**[baggy@localhost etc]$** sudo mkdir /opt/smokeping/img  
**[baggy@localhost etc]$** sudo mkdir /opt/smokeping/data  
**[baggy@localhost etc]$** sudo mkdir /opt/smokeping/var  
**[baggy@localhost etc]$** sudo mkdir /opt/smokeping/cache  
**[baggy@localhost etc]$** sudo chown -R apache:apache /opt/smokeping/img/  
**[baggy@localhost etc]$** sudo chown -R apache:apache /opt/smokeping/cache/  
**[baggy@localhost etc]$** sudo chmod 600 /opt/smokeping/etc/smokeping_secrets  
**[baggy@localhost etc]$** sudo chmod 600 /opt/smokeping/etc/smokeping_secrets.dist  
**[baggy@localhost etc]$** sudo mkdir /var/www/smokeping  
**[baggy@localhost etc]$** sudo cp -R /opt/smokeping/htdocs/\* /var/www/smokeping/  
**[baggy@localhost etc]$** sudo mv /var/www/smokeping/smokeping.fcgi.dist /var/www/smokeping/smokeping.fcgi  
**[baggy@localhost etc]$** sudo chown -R apache:apache /var/www/smokeping  

**[baggy@localhost etc]$** sudo nano /etc/httpd/conf.d/smokeping.conf  
*Add the below lines to the file and save:*
```
    Alias /smokeping "/var/www/smokeping"
    <Directory /var/www/smokeping>
    Options Indexes FollowSymLinks MultiViews ExecCGI
    AllowOverride All
    Order allow,deny
    Allow from all
    DirectoryIndex smokeping.fcgi
    </Directory>
```
**[baggy@localhost etc]$** sudo nano /opt/smokeping/etc/config  
*Find and edit the line starting with cgiurl and add your ip address, 192.168.56.4 is my host ip that I added:*
```
cgiurl   = http://192.168.56.4/smokeping.cgi
```
**[baggy@localhost etc]** sudo nano /etc/selinux/config  
*Edit the SELINUX line to be disabled like below*
```
SELINUX=disabled
```
**[baggy@localhost etc]$** firewall-cmd --add-service=http

**[baggy@localhost etc]$** sudo service httpd restart

Use web browser to open http\://192.168.56.4/smokeping.cgi

#### Note: If you experience broken images on the web interface edit the config file as described below  
**[baggy@localhost etc]$** sudo nano  /opt/smokeping/etc/config  
*Edit the imgurl value from cache to /smokeping*  
```imgurl   = /smokeping```
