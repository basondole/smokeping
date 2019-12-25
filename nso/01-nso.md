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
<pre>
<enabled>true</enabled>
<space-completion><enabled>false</enabled></space-completion>
<ignore-leading-whitespace>true</ignore-leading-whitespace>
<auto-wizard><enabled>false</enabled></auto-wizard>
</pre>

