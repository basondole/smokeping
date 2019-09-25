# Installation on debian distributions  
Here we install bird on linux box running ubuntu 18 and configure local BGP using BIRD between the box and a junos router.

The precedding numbers on each step below correlate to the timestamps on the snapshots

* **1958:16** Install bird `sudo apt install bird`  
* **1958:21** Check the if ip forwarding is enable in the file `/etc/sysctl.conf` in this case the output lines are starting with `#` that means they are commented and hence ip forwarding is disabled  
* **1958:51** Use nano editor or any editor of your choice to edit the file `/etc/sysctl.conf` and remove the `#` preceeding the lines  
<pre>
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1
</pre>

* **1958:51** Verify the lines have been uncommented and there is no preceeding `#`  
* **2013:31** Make a backup of the default bird configuration you may need it in the future

![installbird](https://user-images.githubusercontent.com/50369643/63584660-117a9500-c5a6-11e9-8698-2b115695253f.png)
