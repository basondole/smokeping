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
ERROR Verification requires Python version 2.7.4 or later.
ERROR
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
% No entries found.
admin@ncs# packages reload

>>> System upgrade is starting.
>>> Sessions in configure mode must exit to operational mode.
>>> No configuration changes can be performed until upgrade has completed.
>>> System upgrade has been cancelled.
<b>Error: User java class "com.tailf.packages.ned.ios.UpgradeNedId" exited with status 127</b>
admin@ncs# show packages
% No entries found.
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

Loggin to the ncs and reload the packages
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
