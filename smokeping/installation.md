# Installation on CentOS 7
**[baggy@localhost ~]$**  
    sudo yum install epel-release -y  
    sudo yum install mod_fcgid httpd httpd-devel rrdtool perl-CGI-SpeedyCGI fping rrdtool-perl perl perl-Sys-Syslog -y  
    sudo yum install perl-CPAN perl-local-lib perl-Time-HiRes –y  
    sudo yum groupinstall "Development tools" –y  
    sudo wget http\://oss\.oetiker\.ch/smokeping/pub/smokeping-2.6.11.tar.gz  
    tar -zxvf smokeping-2.6.11.tar.gz  
    cd smokeping-2.6.11/setup  

**[baggy@localhost setup]$**  
    sudo ./build-perl-modules.sh  
    sudo mkdir /opt/smokeping  
    cd ..  

**[baggy@localhost smokeping-2.6.11]$**  
    sudo cp -r thirdparty /opt/smokeping/  
    ./configure --prefix=/opt/smokeping  
    sudo /usr/bin/gmake install  
    cd /opt/smokeping/etc/  

**[baggy@localhost etc]$**  
    sudo cp /opt/smokeping/etc/basepage.html.dist /opt/smokeping/etc/basepage.html  
    for FOO in \*.dist; do sudo cp \$FOO \`basename \$FOO .dist\`\;done  
    sudo mkdir /opt/smokeping/img  
    sudo mkdir /opt/smokeping/data  
    sudo mkdir /opt/smokeping/var  
    sudo mkdir /opt/smokeping/cache  
    sudo chown -R apache:apache /opt/smokeping/img/  
    sudo chown -R apache:apache /opt/smokeping/cache/  
    sudo chmod 600 /opt/smokeping/etc/smokeping_secrets  
    sudo chmod 600 /opt/smokeping/etc/smokeping_secrets.dist  
    sudo mkdir /var/www/smokeping  
    sudo cp -R /opt/smokeping/htdocs/\* /var/www/smokeping/  
    sudo mv /var/www/smokeping/smokeping.fcgi.dist /var/www/smokeping/smokeping.fcgi  
    sudo chown -R apache:apache /var/www/smokeping  
    sudo nano /etc/httpd/conf.d/smokeping.conf 
    
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
**[baggy@localhost etc]$**  
    sudo nano /opt/smokeping/etc/config  
    *Find and edit the line starting with cgiurl and add your ip address, for my case my IP is 192.168.56.4 and save*
```
    cgiurl   = http://192.168.56.4/smokeping.cgi
```

**[baggy@localhost etc]**  
    sudo nano /etc/selinux/config  
    *Edit the SELINUX line to be disabled like below*
```
    SELINUX=disabled
```
**[baggy@localhost etc]$**  
    firewall-cmd --add-service=http  
    sudo service httpd restart

Use web browser to open http\://192.168.56.4/smokeping.cgi

