# Troubleshooting

Do this after every change you make to the config file

**[baggy@localhost ~]$**  
sudo service httpd restart  
sudo /opt/smokeping/bin/smokeping

  
If you experience broken images on the web interface edit the config file as described below  

**[baggy@localhost etc]$** sudo nano  /opt/smokeping/etc/config  
*Edit the imgurl value from cache to /smokeping*  
```imgurl   = /smokeping```
