# Configuring the BIRD

The precedding numbers on each step below correlate to the timestamps on the snapshots

* **2301:28** Edit the `/etc/bird/bird.conf` to specify your bgp and neighbor parameters and prefixes to announce.  
* **2301:33** Sample configuration I have put in the `/etc/bird/bird.conf`  
> I have used awk to only print the lines I have modified in the file

* **2301:36** Start the bird service on the machine. 
> If there is something wrong in the `/etc/bird/bird.conf` the service will not start. In such case the terminal will suggest some methods to follow so as to view the logs  

* **2301:48** Verify the service is started

![installbird2](https://user-images.githubusercontent.com/50369643/63585029-dcbb0d80-c5a6-11e9-9975-cd18b2475c95.png)

* **2315:06** Verify BGP by getting into the bird client issue command birdc as super user  
Use the command `show protocols all bgp1` to check the bgp summary. We see the configured neighbour 192.168.56.36 is up and we are receiving 1 prefix.
To view the prefix issue the command `show route protocol bgp1` This shows we are receiving a default route  
* **2315:54** Verify the host machine routing table. We see the default route we receive from the bgp neighbour 192.168.56.36 is installed in the local machine routing table with the next-hop 192.168.56.2  

![installbird3](https://user-images.githubusercontent.com/50369643/63585144-2277d600-c5a7-11e9-8932-6ee709a653bd.png)

* **2335:56** Login to the router 192.168.56.36 we see the bgp session is up. The router is advertising the default route to the machine running bird and receiving the two prefixes as we announce them on the bird configuration `@2301:33`

![installingbird4](https://user-images.githubusercontent.com/50369643/63585434-c82b4500-c5a7-11e9-982b-bf6ef58b9df6.png)
