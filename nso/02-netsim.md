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

