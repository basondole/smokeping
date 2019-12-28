# Netsim

Netsim offers emulated devices that can be used with the NSO mainly for testing and development

### Creating an emulated device
<pre>
basondole@netbox:~/nso/ncs-run$ ls packages/
cisco-ios-cli-3.8  cisco-iosxr-cli-3.5  juniper-junos-nc-3.0
cisco-ios-cli-3.0  cisco-iosxr-cli-3.0  cisco-nx-cli-3.0
basondole@netbox:~/nso/ncs-run$ cd myownnetsim/
basondole@netbox:~/nso/ncs-run/myownnetsim$
basondole@netbox:~/nso/ncs-run/myownnetsim$ mkdir iosxr
basondole@netbox:~/nso/ncs-run/myownnetsim$ cd iosxr
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr$ ncs-netsim create-device cisco-iosxr-cli-3.5 iosxr
<b>DEVICE iosxr CREATED</b>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/$ ls
netsim
</pre>

To start the emulated device
<pre>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ cd netsim/
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ ls
iosxr  README.netsim
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ ncs-netsim start
DEVICE iosxr OK STARTED
</pre>

To check if the device is running
<pre>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ ncs-netsim is-alive iosxr
DEVICE iosxr OK
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$
</pre>

To get the port being used by device
<pre>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ ncs-netsim list
ncs-netsim list for  /home/basondole/nso/ncs-run/myownnetsim/iosxr/netsim

name=iosxr netconf=12022 snmp=11022 ipc=5010 <b>cli=10022</b> \
dir=/home/basondole/nso/ncs-run/myownnetsim/iosxr/netsim/iosxr/iosxr

basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ ncs-netsim get-port iosxr cli
<b>10022</b>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$
</pre>

Connecting to the device
<pre>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ ncs-netsim cli-i iosxr

admin connected from 192.168.56.1 using ssh on netbox
netbox> enable
netbox# show version
Cisco IOS XR Software, NETSIM
netbox# exit
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$
</pre>

### Adding the device to the nso
<pre>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ ncs_cli -C

basondole connected from 192.168.56.1 using ssh on netbox
basondole@ncs# config
Entering configuration mode terminal
basondole@ncs(config)# devices device netsim address 127.0.0.1 port 10022 
basondole@ncs(config-device-netsim)# device-type cli ned-id cisco-iosxr-cli-3.5 protocol ssh
basondole@ncs(config-device-netsim)# authgroup default state admin-state unlocked
basondole@ncs(config-device-netsim)# commit
basondole@ncs(config-device-netsim)# top
basondole@ncs(config)# devices device netsim ssh fetch-host-keys
<b>result failed</b>
<b>info internal error</b>
basondole@ncs(config)# exit
basondole# exit
</pre>

We have run into an error when fetching the host key.
To fix this we have to make sure the right keys are existing in the emulated device ssh directory.
The easy fix is to copy the keys from `$NCS_DIR/netsim/confd/etc/confd/ssh/` to our emulated device ssh directory

<pre>
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ cp $NCS_DIR/netsim/confd/etc/confd/ssh/ssh_host_rsa_key.pub iosxr/iosxr/ssh/ssh_host_rsa_key.pub
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ diff $NCS_DIR/netsim/confd/etc/confd/ssh/ssh_host_rsa_key.pub iosxr/iosxr/ssh/ssh_host_rsa_key.pub
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ cp $NCS_DIR/netsim/confd/etc/confd/ssh/ssh_host_rsa_key iosxr/iosxr/ssh/ssh_host_rsa_key
basondole@netbox:~/nso/ncs-run/myownnetsim/iosxr/netsim$ diff $NCS_DIR/netsim/confd/etc/confd/ssh/ssh_host_rsa_key iosxr/iosxr/ssh/ssh_host_rsa_key
</pre>

Then we restart the nso and the emulated device then fetch the keys again
<pre>
basondole@netbox:~/nso/ncs-run$ ncs --stop
basondole@netbox:~/nso/ncs-run$ ncs-netsim stop --dir myownnetsim/iosxr/netsim
<b>DEVICE iosxr STOPPED</b>
basondole@netbox:~/nso/ncs-run$ ncs
basondole@netbox:~/nso/ncs-run$ ncs-netsim start --dir myownnetsim/iosxr/netsim
<b>DEVICE iosxr OK STARTED</b>
basondole@netbox:~/nso/ncs-run$ ncs_cli -C -u admin

admin connected from 192.168.56.1 using ssh on netbox
admin@ncs# conf
Entering configuration mode terminal
admin@ncs(config)# devices device netsim ssh fetch-host-keys
<b>result updated</b>
fingerprint {
    algorithm ssh-rsa
    value ff:18:c0:6b:8b:fa:04:d4:8c:ed:91:ce:25:66:e9:df
}
admin@ncs(config)#
</pre>

### Adding multiple devices
Verifying the NEDs available
<pre>
basondole@netbox:~/nso/ncs-run$ ls
logs  ncs-cdb  ncs.conf  packages  README.ncs  scripts  state  storedstate  target
basondole@netbox:~/nso/ncs-run$ ls packages/
cisco-ios-cli-3.8  cisco-iosxr-cli-3.5  juniper-junos-nc-3.0
cisco-ios-cli-3.0  cisco-iosxr-cli-3.0  cisco-nx-cli-3.0
basondole@netbox:~/nso/ncs-run$
</pre>

Creating the devices
<pre>
basondole@netbox:~/nso/ncs-run$ ncs-netsim create-device cisco-ios-cli-3.8 netsim-ios-00
DEVICE netsim-ios-00 CREATED
basondole@netbox:~/nso/ncs-run$ ncs-netsim add-device cisco-iosxr-cli-3.5 netsim-xr-00
DEVICE netsim-xr-00 CREATED
basondole@netbox:~/nso/ncs-run$ ncs-netsim add-device juniper-junos-nc-3.0 netsim-junos-00
DEVICE netsim-junos-00 CREATED
basondole@netbox:~/nso/ncs-run$ ls
logs  ncs-cdb  ncs.conf  <b>netsim</b>  packages  README.ncs  scripts  state  storedstate  target
basondole@netbox:~/nso/ncs-run$
</pre>
> Note we initially created one device using `create-device`. The following devices were added using `add-device`

Starting the devices
<pre>
basondole@netbox:~/nso/ncs-run$ cd netsim
basondole@netbox:~/nso/ncs-run/netsim$ ncs-netsim start netsim-ios-00
<b>DEVICE netsim-ios-00 OK STARTED</b>
basondole@netbox:~/nso/ncs-run/netsim$ ncs-netsim start netsim-xr-00
<b>DEVICE netsim-xr-00 OK STARTED</b>
basondole@netbox:~/nso/ncs-run/netsim$ ncs-netsim start netsim-junos-00
<b>DEVICE netsim-junos-00 OK STARTED</b>
basondole@netbox:~/nso/ncs-run/netsim$ ncs-netsim list
ncs-netsim list for  /home/basondole/nso/ncs-run/netsim

name=netsim-ios-00 netconf=12022 snmp=11022 ipc=5010 cli=10022 dir=/home/basondole/nso/ncs-run/netsim/netsim-ios-00/netsim-ios-00
name=netsim-xr-00 netconf=12023 snmp=11023 ipc=5011 cli=10023 dir=/home/basondole/nso/ncs-run/netsim/netsim-xr-00/netsim-xr-00
name=netsim-junos-00 netconf=12024 snmp=11024 ipc=5012 cli=10024 dir=/home/basondole/nso/ncs-run/netsim/netsim-junos-00/netsim-junos-00
basondole@netbox:~/nso/ncs-run/netsim$
</pre>

Connecting to the devices via ssh
<pre>
basondole@netbox:~/nso/ncs-run/netsim$ ssh admin@127.0.0.1 -p 10022
admin@127.0.0.1's password:

admin connected from 127.0.0.1 using ssh on netbox
<b>netsim-ios-00> exit</b>
Connection to 127.0.0.1 closed.
basondole@netbox:~/nso/ncs-run/netsim$ ssh admin@127.0.0.1 -p 10023
admin@127.0.0.1's password:

admin connected from 127.0.0.1 using ssh on netbox
<b>netbox# exit</b>
Connection to 127.0.0.1 closed.
basondole@netbox:~/nso/ncs-run/netsim$ ssh admin@127.0.0.1 -p 10024
admin@127.0.0.1's password:

admin connected from 127.0.0.1 using ssh on netbox
<b>admin@netsim-junos-00>exit</b>
Connection to 127.0.0.1 closed.
basondole@netbox:~/nso/ncs-run/netsim$
</pre>

### Bulk export of devices to the nso
When we have multiple emulated devices we can easily export them to the nso easing the urden of addin the devices one by one 

<pre>
basondole@netbox:~/nso/ncs-run/netsim$ ncs-netsim ncs-xml-init > devices.xml
/home/basondole/nso/nso-5.1.0.1/bin/ncs-netsim: line 895: xsltproc: <b>command not found</b>
/home/basondole/nso/nso-5.1.0.1/bin/ncs-netsim: line 895: xsltproc: <b>command not found</b>
/home/basondole/nso/nso-5.1.0.1/bin/ncs-netsim: line 895: xsltproc: <b>command not found</b>
/home/basondole/nso/nso-5.1.0.1/bin/ncs-netsim: line 895: xsltproc: <b>command not found</b>
basondole@netbox:~/nso/ncs-run/netsim$ ls
<b>devices.xml</b>  netsim-ios-00  netsim-junos-00  netsim-nx-00  netsim-xr-00  README.netsim

basondole@netbox:~/nso/ncs-run/netsim$ ncs_load -l -m devices.xml
ncs_load: 690: maapi_apply_trans_flags(sock, tid, 0, aflags) failed: Unsatisfied must constraint (41): \
/ncs:devices/device{netsim-ios-00}/device-type : <b>must configure one of: snmp, cli, generic, netconf</b>
basondole@netbox:~/nso/ncs-run/netsim$
</pre>

To uderstand the error we have to check the contents of the created file `devices.xml`
```
basondole@netbox:~/nso/ncs-run/netsim$ less devices.xml
<devices xmlns="http://tail-f.com/ns/ncs">
   <device>
     <name>netsim-ios-00</name>
     <address>127.0.0.1</address>
     <port>10022</port>
     <ssh>
       <host-key>
         <algorithm>ssh-rsa</algorithm>
         <key-data>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/23... basondole@netbox</key-data>
       </host-key>
     </ssh>
     <state>
       <admin-state>unlocked</admin-state>
     </state>
     <authgroup>default</authgroup>
     <device-type>
       <cli>

       </cli>
     </device-type>
   </device>
```

We see the device-type is not defined. We therefore have to edit it manually. To get the device type you can add the device manully to the nso
```
basondole@netbox:~/nso/ncs-run/netsim$ ncs_cli -C
basondole@ncs(config)# show configuration | exclude no
devices device test
 address   test
 authgroup default
 device-type cli ned-id cisco-nx-cli-3.0
 device-type cli protocol ssh
 config
 !
!
basondole@ncs(config)# commit dry-run outformat xml
result-xml {
    local-node {
        data <devices xmlns="http://tail-f.com/ns/ncs">
               <device>
                 <name>test</name>
                 <address>test</address>
                 <authgroup>default</authgroup>
                 <device-type>
                   <cli>
                     <ned-id xmlns:cisco-nx-cli-3.0="http://tail-f.com/ns/ned-id/cisco-nx-cli-3.0">cisco-nx-cli-3.0:cisco-nx-cli-3.0</ned-id>
                     <protocol>ssh</protocol>
                   </cli>
                 </device-type>
               </device>
             </devices>
    }
}
basondole@ncs(config)# end
Uncommitted changes found, commit them? [yes/no/CANCEL] no
basondole@ncs# exit
```

We then copy the `device-type` and paste in the corresponding section in the `device.xml file`
which then becomes

```
basondole@netbox:~/nso/ncs-run/netsim$ less devices.xml
devices xmlns="http://tail-f.com/ns/ncs">
   <device>
     <name>netsim-ios-00</name>
     <address>127.0.0.1</address>
     <port>10022</port>
     <ssh>
       <host-key>
         <algorithm>ssh-rsa</algorithm>
         <key-data>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/23... basondole@netbox</key-data>
       </host-key>
     </ssh>
     <state>
       <admin-state>unlocked</admin-state>
     </state>
     <authgroup>default</authgroup>
     <device-type>
       <cli>
         <ned-id xmlns:cisco-ios-cli-3.8="http://tail-f.com/ns/ned-id/cisco-ios-cli-3.8">cisco-ios-cli-3.8:cisco-ios-cli-3.8</ned-id>
         <protocol>ssh</protocol>
       </cli>
     </device-type>
   </device>
 ```
 
 Loading the devices to the nso
 <pre>
basondole@netbox:~/nso/ncs-run/netsim$ ncs_load -l -m devices.xml
basondole@netbox:~/nso/ncs-run/netsim$ ncs_cli -C

basondole connected from 192.168.56.1 using ssh on netbox
basondole@ncs# show devices brief
NAME             ADDRESS        DESCRIPTION  NED ID
-----------------------------------------------------------------
netsim-ios-00    127.0.0.1      -            cisco-ios-cli-3.8
netsim-junos-00  127.0.0.1      -            juniper-junos-nc-3.0
netsim-nx-00     127.0.0.1      -            cisco-nx-cli-3.0
netsim-xr-00     127.0.0.1      -            cisco-iosxr-cli-3.5
basondole@ncs#
</pre>


### Adding devices to a group
<pre>
basondole@netbox:~/nso/ncs-run/netsim$ ncs_cli -C -u admin

admin connected from 192.168.56.1 using ssh on netbox
admin@ncs# config
admin@ncs(config)# devices device-group netsim
admin@ncs(config-device-group-netsim)# device-name netsim-ios-00
admin@ncs(config-device-group-netsim)# device-name netsim-xr-00
admin@ncs(config-device-group-netsim)# device-name netsim-nx-00
admin@ncs(config-device-group-netsim)# device-name netsim-junos-00
admin@ncs(config-device-group-netsim)# top
admin@ncs(config)# show configuration
devices device-group netsim
 device-name [ netsim-ios-00 netsim-junos-00 netsim-nx-00 netsim-xr-00 ]
!
admin@ncs(config)# commit
Commit complete.
admin@ncs(config)# do show devices device-group member
NAME    MEMBER
---------------------------------------------------------------------
netsim  [ netsim-ios-00 netsim-junos-00 netsim-nx-00 netsim-xr-00 ]

admin@ncs(config)# do devices device-group netsim sync-from
sync-result {
    device netsim-ios-00
    result true
}
sync-result {
    device netsim-junos-00
    result true
}
sync-result {
    device netsim-nx-00
    result true
}
sync-result {
    device netsim-xr-00
    result true
}
admin@ncs(config)#
</pre>

