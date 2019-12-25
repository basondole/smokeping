# NSO
NSO enabled by Tail-f provides end-to-end automation to design and deliver services much faster.
It seamlessly integrates all of your infrastructure across different technologies, vendors.
Learn more at https://developer.cisco.com/site/nso/

## Downloading the NSO
Get the link for downloading nso from Cisco website then download the nso package

<pre>
basondole@netbox:~$ mkdir nso
basondole@netbox:~$ cd nso
basondole@netbox:~/nso$ wget "https://devnet-filemedia-download.s3.amazonaws.com/119b2bc7-dbf6-49a1-974d-0a5610e41390/nso-5.1.0.1.linux.x86_64.signed.bin?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAXOWDCPZVVCGUYIRZ%2F20191113%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20191113T130414Z&X-Amz-Expires=900&X-Amz-SignedHeaders=host&X-Amz-Signature=f66d3a819f755e24dfdb08844fb2e9d5fd676f28a518a3ab922347b302cda0b4" -O nso-5.1.0.1
basondole@netbox:~/nso$ ls
nso-5.1.0.1 
basondole@netbox:~/nso$
</pre>

## Extract the package
<pre>
basondole@netbox:~/nso$ sh nso-5.1.0.1
Unpacking...
Verifying signature...
Downloading CA certificate from http://www.cisco.com/security/pki/certs/crcam2.cer ...
Successfully downloaded and verified crcam2.cer.
Downloading SubCA certificate from http://www.cisco.com/security/pki/certs/innerspace.cer ...
Successfully downloaded and verified innerspace.cer.
Successfully verified root, subca and end-entity certificate chain.
Successfully fetched a public key from tailf.cer.
Successfully verified the signature of nso-5.1.0.1.linux.x86_64.installer.bin using tailf.cer

basondole@netbox:~/nso$ ls
cisco_x509_verify_release.py  nso-5.1.0.1.linux.x86_64.installer.bin            README.signature
nso-5.1.0.1                   nso-5.1.0.1.linux.x86_64.installer.bin.signature  tailf.cer
basondole@netbox:~/nso$
</pre>

## Installation
In this system, we'll install the nso in the home directory

<pre>
basondole@netbox:~/nso$ sh nso-5.1.0.1.linux.x86_64.installer.bin $HOME/nso-5.1.0.1
INFO  Using temporary directory /tmp/ncs_installer.2786 to stage NCS installation bundle
INFO  Unpacked ncs-5.1.0.1 in /home/basondole/nso-5.1.0.1
INFO  Found and unpacked corresponding DOCUMENTATION_PACKAGE
INFO  Found and unpacked corresponding EXAMPLE_PACKAGE
INFO  Generating default SSH hostkey (this may take some time)
INFO  SSH hostkey generated
INFO  Environment set-up generated in /home/basondole/nso-5.1.0.1/ncsrc
INFO  NCS installation script finished
INFO  Found and unpacked corresponding NETSIM_PACKAGE
INFO  NCS installation complete

basondole@netbox:~/nso$
</pre>

#### Incase of a python error 
<pre>
basondole@netbox:~/nso$ sh nso-5.1.0.1
Unpacking...
<b>ERROR Verification requires Python version 2.7.4 or later.
ERROR</b>
</pre>
To overcome this do a python install then extrat the file again
<pre>
basondole@netbox:~/nso$ sudo apt install python
.
.
basondole@netbox:~/nso$ sh nso-5.1.0.1.linux.x86_64.installer.bin $HOME/nso-5.1.0.1
</pre>

## Running the nso

<pre>
basondole@netbox:~/nso$ cd ..
basondole@netbox:~$ ls
nso  nso-5.1.0.1
basondole@netbox:~$ source $HOME/nso-5.1.0.1/ncsrc
basondole@netbox:~$ ncs-setup --dest $HOME/ncs-run
basondole@netbox:~$ ls
ncs-run  nso  nso-5.1.0.1
basondole@netbox:~$ cd ncs-run/
basondole@netbox:~/ncs-run$ ls
logs  ncs-cdb  ncs.conf  packages  README.ncs  scripts  state
basondole@netbox:~/ncs-run$ ncs ! takes a minute to start
basondole@netbox:~/ncs-run$ ncs --status
basondole@netbox:~/ncs-run$ ncs --version
5.1.0.1
basondole@netbox:~/ncs-run$ ncs --status | grep status
status: started
basondole@netbox:~/ncs-run$
</pre>

## Accessing the nso
The NSO offers a frontend UI which can be accessed via a web browser via `http://192.168.56.20:8080/login.html`  
Where `192.168.56.20` is my server address  
The default login credentials:  
`username: admin`  
`password: admin`

### To access the nso via CLI
<pre>
basondole@netbox:~/ncs-run$ ncs_cli -u admin -C

admin connected from 192.168.56.1 using ssh on netbox
admin@ncs# exit
basondole@netbox:~/ncs-run$
</pre>


## Configuration
To enable pasting of multiple lines of text in the ncs cli add below lines in the ncs config file

`basondole@netbox:~/ncs-run$ nano ncs.conf`
```
<enabled>true</enabled>
<space-completion><enabled>false</enabled></space-completion>
<ignore-leading-whitespace>true</ignore-leading-whitespace>
<auto-wizard><enabled>false</enabled></auto-wizard>
```

To offer support for a range of multivendor devices, NSO uses Network Element Drivers (NEDs).
Using NEDs, NSO makes device configuration commands available over a network wide, multivendor Command Line Interface (CLI), APIs, and user interface  
Learn more at https://www.cisco.com/c/en/us/products/collateral/cloud-systems-management/network-services-orchestrator/datasheet-c78-734669.html

To verify the pre installed NEDs on your system
<pre>
basondole@netbox:~/ncs-run$ cd $NCS_DIR
basondole@netbox:~/nso-5.1.0.1$ ls packages/
lsa  neds  services  tools
basondole@netbox:~/nso-5.1.0.1$ ls packages/neds/
a10-acos-cli-3.0  cisco-ios-cli-3.0  cisco-iosxr-cli-3.0  cisco-nx-cli-3.0   juniper-junos-nc-3.0
alu-sr-cli-3.4    cisco-ios-cli-3.8  cisco-iosxr-cli-3.5  dell-ftos-cli-3.0
basondole@netbox:~/nso-5.1.0.1$
</pre>

To verif whether the packages are loaded in the ncs
<pre>
basondole@netbox:~/ncs-run$ ncs_cli -u admin -C
admin connected from 192.168.56.1 using ssh on netbox
admin@ncs# show packages
% No entries found.
admin@ncs# exit
</pre>

If they are not loaded as seen above you can issue a reload command in the ncs
<pre>
basondole@netbox:~/ncs-run$ ncs_cli -u admin -C

admin connected from 192.168.56.1 using ssh on netbox
admin@ncs# show packages
<b>% No entries found.</b>
admin@ncs# packages reload

>>> System upgrade is starting.
>>> Sessions in configure mode must exit to operational mode.
>>> No configuration changes can be performed until upgrade has completed.
>>> System upgrade has been cancelled.
<b>Error: User java class "com.tailf.packages.ned.ios.UpgradeNedId" exited with status 127</b>
admin@ncs# show packages
<b>% No entries found.</b>
admin@ncs# exit
</pre>

If you run into this error confirm you have java installed and if not install java
<pre>
basondole@netbox:~/ncs-run$ java -version

Command 'java' not found, but can be installed with:

sudo apt install default-jre
sudo apt install openjdk-11-jre-headless
sudo apt install openjdk-8-jre-headless

basondole@netbox:~/ncs-run$ sudo apt-get update -y
.
.
basondole@netbox:~/ncs-run$ sudo apt-get install openjdk-11-jre -y
.
.
basondole@netbox:~/ncs-run$ sudo apt-get install ant -y
.
.
basondole@netbox:~/ncs-run$ java -version
openjdk version "11.0.5" 2019-10-15
OpenJDK Runtime Environment (build 11.0.5+10-post-Ubuntu-0ubuntu1.118.04)
OpenJDK 64-Bit Server VM (build 11.0.5+10-post-Ubuntu-0ubuntu1.118.04, mixed mode, sharing)
</pre>

Also confirm the packages are available on the directory you are running NSO from
in my case I'm running NSO from `~/ncs-run`
<pre>
basondole@netbox:~/ncs-run$ ls packages/
basondole@netbox:~/ncs-run$
</pre>

If the package directory is empty copy the NEDs from the `$NCS_DIR` directory
<pre>
basondole@netbox:~/ncs-run$ cp -r ~/nso-5.1.0.1/packages/neds/* ./packages/
basondole@netbox:~/ncs-run$ ls packages/
cisco-ios-cli-3.0  cisco-iosxr-cli-3.0  cisco-nx-cli-3.0
cisco-ios-cli-3.8  cisco-iosxr-cli-3.5  juniper-junos-nc-3.0
</pre>

Login to the ncs and reload the packages
<pre>
basondole@netbox:~/ncs-run$ ncs_cli -u admin -C
admin@ncs# packages reload
.
reload-result {
    package cisco-iosxr-cli-3.5
    result false
    <b>info --ERROR--</b>
}
reload-result {
    package cisco-nx-cli-3.0
    result false
    <b>info --ERROR--</b>
}
reload-result {
    package dell-ftos-cli-3.0
    result false
    <b>info --ERROR--</b>
}
reload-result {
    package juniper-junos-nc-3.0
    <b>result true</b>
}
basondole@ncs# show packages package oper-status
                                                                                         PACKAGE
                          PROGRAM                                                        META     FILE
                          CODE     JAVA           BAD NCS  PACKAGE  PACKAGE  CIRCULAR    DATA     LOAD   ERROR
NAME                  UP  ERROR    UNINITIALIZED  VERSION  NAME     VERSION  DEPENDENCY  ERROR    ERROR  INFO
----------------------------------------------------------------------------------------------------------------
cisco-ios-cli-3.0     -   -        X              -        -        -        -           -        -      -
cisco-nx-cli-3.0      -   -        X              -        -        -        -           -        -      -
cisco-iosxr-cli-3.0   -   -        X              -        -        -        -           -        -      -
dell-ftos-cli-3.0     -   -        X              -        -        -        -           -        -      -
juniper-junos-nc-3.0  X   -        -              -        -        -        -           -        -      -

basondole@ncs# exit
</pre>

From above we see we had errors loading a couple of NEDs with `java unitialized` status
The issue here is very likely related to the JavaVM, since all the Java packages are failing,
while the Junos NETCONF NED (which doesn't use any Java) is fine.
Since we have quite a few NEDs, the issue is almost certainly that the JavaVM is out of memory/heap space.

To check the java-vm log on ncs   `basondole@netbox:~/ncs-run$ less logs/ncs-java-vm.log`

To fix the memory problem in my case since my server has 2GB of RAM, I assigned 1GB of memory to java.  
`basondole@netbox:~/ncs-run$ export NCS_JAVA_VM_OPTIONS=-Xmx1G`  
You can add this to your `.bash_profile` so that it is done automatically everytime you log in 

We then relaunch the ncs
<pre>
basondole@netbox:~/ncs-run$ ncs --stop
basondole@netbox:~/ncs-run$ ncs
basondole@netbox:~/ncs-run$ ncs_cli -C

basondole connected from 192.168.56.1 using ssh on netbox

basondole@ncs# packages reload

>>> System upgrade is starting.
>>> Sessions in configure mode must exit to operational mode.
>>> No configuration changes can be performed until upgrade has completed.
>>> System upgrade has completed successfully.
.
.
reload-result {
    package cisco-iosxr-cli-3.0
    <b>result true</b>
}
reload-result {
    package cisco-iosxr-cli-3.5
    <b>result true</b>
}
reload-result {
    package juniper-junos-nc-3.0
    <b>result true</b>
}
basondole@ncs#
basondole@ncs# show packages package oper-status
                                                                                         PACKAGE
                          PROGRAM                                                        META     FILE
                          CODE     JAVA           BAD NCS  PACKAGE  PACKAGE  CIRCULAR    DATA     LOAD   ERROR
NAME                  UP  ERROR    UNINITIALIZED  VERSION  NAME     VERSION  DEPENDENCY  ERROR    ERROR  INFO
----------------------------------------------------------------------------------------------------------------
cisco-ios-cli-3.0     X   -        -              -        -        -        -           -        -      -
cisco-ios-cli-3.8     X   -        -              -        -        -        -           -        -      -
cisco-iosxr-cli-3.0   X   -        -              -        -        -        -           -        -      -
cisco-iosxr-cli-3.5   X   -        -              -        -        -        -           -        -      -
cisco-nx-cli-3.0      X   -        -              -        -        -        -           -        -      -
juniper-junos-nc-3.0  X   -        -              -        -        -        -           -        -      -

basondole@ncs# exit
basondole@netbox:~/ncs-run$
</pre>

## Adding the auth group to the ncs
Before we can add devices in the ncs we have to define an authentication group

<pre>
admin@ncs# config
admin@ncs(config)# devices authgroups group GROUP01
admin@ncs(config-group-GROUP01)# default-map remote-name fisi
admin@ncs(config-group-GROUP01)# default-map remote-password fisi123
admin@ncs(config-group-GROUP01)# top
admin@ncs(config)# commit check
<b>Validation complete</b>
admin@ncs(config)# show configuration diff
+devices authgroups group GROUP01
+ default-map remote-name fisi
+ default-map remote-password $8$1SgUsPkoEaFvTwK02flfv5Ta5ut9WBf+I1m+OaTo8vQ=
+!
admin@ncs(config)# commit
<b>Commit complete.</b>

admin@ncs(config)# do show configuration commit list
2019-12-24 13:53:45
SNo. ID       User       Client      Time Stamp          Label       Comment
~~~~ ~~       ~~~~       ~~~~~~      ~~~~~~~~~~          ~~~~~       ~~~~~~~
1000 10002    admin      cli         2019-12-24 13:51:41
1000 10001    system     system      2019-11-13 13:42:45
</pre>


## Configuring devices for nso
> Configuration is pulled from devices I used on my presentation at Pycon Tanzania Dec 2019 excuse the use of pycon for hostnames  

Cisco IOS XR
<pre>
RP/0/0/CPU0:pycon-iosxr(config)#show configuration 
Tue Dec 24 14:10:36.560 UTC
Building configuration...
username fisi
 secret 5 $1$UV0J$uNLTpu2nr6K2ZhY7z2cks/
ssh server v2
ssh server netconf port 830
ssh server logging
netconf-yang agent ssh
RP/0/0/CPU0:pycon-iosxr(config)#commit
RP/0/0/CPU0:pycon-iosxr(config)#exit
RP/0/0/CPU0:pycon-iosxr#crypto key generate rsa 
</pre>

JunOS
<pre>
fisi@pycon-junos> show configuration system services
ssh;
netconf {
    ssh;
}

fisi@pycon-junos> show configuration system login user fisi
uid 2000;
class super-user;
authentication {
    encrypted-password "$1$ty9HKQjx$n3zBLWY5HgycHOQW2/epX/"; ## SECRET-DATA
}
</pre>

Cisco IOS  
Only configure ssh



## Adding a cisco ios xr device to nso
<pre>
admin@ncs(config)# devices device pycon-iosxr
admin@ncs(config-device-pycon-iosxr)# address 192.168.56.65
admin@ncs(config-device-pycon-iosxr)# authgroup GROUP01
admin@ncs(config-device-pycon-iosxr)# device-type cli ned-id cisco-iosxr-cli-3.5
admin@ncs(config-device-pycon-iosxr)# device-type cli protocol ssh
admin@ncs(config-device-pycon-iosxr)# state admin-state unlocked
admin@ncs(config-device-pycon-iosxr)# top
admin@ncs(config)# commit check
Validation complete
admin@ncs(config)# show configuration diff
+devices device pycon-iosxr
+ address   192.168.56.65
 !
+devices authgroups group GROUP01
+ default-map remote-name fisi
+ default-map remote-password $8$1SgUsPkoEaFvTwK02flfv5Ta5ut9WBf+I1m+OaTo8vQ=
+!
 devices device pycon-iosxr
+ authgroup GROUP01
+ device-type cli ned-id cisco-iosxr-cli-3.0
+ device-type cli protocol ssh
+ state admin-state unlocked
+ config
+  no ios:service pad
+  no ios:ip domain-lookup
+  no ios:service password-encryption
+  no ios:cable admission-control preempt priority-voice
+  no ios:cable qos permission create
+  no ios:cable qos permission update
+  no ios:cable qos permission modems
+  no ios:ip cef
+  no ios:ip forward-protocol nd
+  no ios:ipv6 source-route
+  no ios:ipv6 cef
+  no nx:feature ssh
+  no nx:feature telnet
+ !
+!
admin@ncs(config)# commit
Commit complete.

admin@ncs(config)# do show running-config | begin pycon
devices device pycon-iosxr
 address   192.168.56.65
 authgroup GROUP01
 device-type cli ned-id cisco-iosxr-cli-3.0
 device-type cli protocol ssh
 state admin-state unlocked
 config
  no ios:service pad
  no ios:ip domain-lookup
  no ios:service password-encryption
  no ios:cable admission-control preempt priority-voice
  no ios:cable qos permission create
  no ios:cable qos permission update
  no ios:cable qos permission modems
  no ios:ip cef
  no ios:ip forward-protocol nd
  no ios:ipv6 source-route
  no ios:ipv6 cef
  no nx:feature ssh
  no nx:feature telnet
.
.

admin@ncs# show devices brief
NAME         ADDRESS        DESCRIPTION  NED ID
------------------------------------------------------------
pycon-iosxr  192.168.56.65  -            cisco-iosxr-cli-3.0

</pre>

After adding the device we fetch its ssh keys and then sync-from so as to sychronise the device config to the ncs database
<pre>
admin@ncs# devices device pycon-iosxr ssh fetch-host-keys
result updated
fingerprint {
    algorithm ssh-rsa
    value f6:46:c1:32:19:24:ff:21:e6:ac:0f:85:78:94:77:40
}

admin@ncs# devices device pycon-iosxr ping
result PING 192.168.56.65 (192.168.56.65) 56(84) bytes of data.
64 bytes from 192.168.56.65: icmp_seq=1 ttl=255 time=6.24 ms

--- 192.168.56.65 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 6.246/6.246/6.246/0.000 ms

admin@ncs# devices device pycon-iosxr sync-from
result true
admin@ncs#
admin@ncs# show devices device pycon-iosxr config
config
 yanglib:modules-state module-set-id 762f393abd3986410711f2cf22587ccd
 yanglib:modules-state module tailf-ned-cisco-ios-xr 2014-02-18
  namespace        http://tail-f.com/ned/cisco-ios-xr
  conformance-type implement
admin@ncs#
</pre>

Now after synching the config from the router to the ncs database, we logon to the router and change configuration
<pre>
RP/0/0/CPU0:pycon-iosxr(config)#username baggy
RP/0/0/CPU0:pycon-iosxr(config-un)#show commi chan diff
Tue Dec 24 14:45:24.827 UTC
Building configuration...
!! IOS XR Configuration 5.3.0
+  username baggy
   !
end

RP/0/0/CPU0:pycon-iosxr(config-un)#commit
</pre>

Then we check with the ncs to see what has changed
<pre>
admin@ncs# devices device pycon-iosxr compare-config
diff
 devices {
     device pycon-iosxr {
         config {
+            cisco-ios-xr:username baggy {
+            }
         }
     }
 }
admin@ncs#
</pre>

We see the config that we added on the router is displayed, this is the diff between the actual config on the device and
the config on the ncs database. Here we can either `sync-from` this device to update the ncs copy of the config or `sync-to`
to push the config from ncs to the device and removing the added config. 
In this case we synced from the device however this was done via web ui

