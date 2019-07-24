# Creating Vagrant Box From Existing VM

## Pre-requisites
- Vagrant is installed on the host machine; download https://www.vagrantup.com/downloads.html
- Virtual box is installed on the host machine; download https://www.virtualbox.org/wiki/Downloads
- Existing VM is installed in virtual box 
- Windows Host machine

# Procedures

1. Assign the first interface of the virtual machine to NAT interface
   This is done via virtual box network adapter settings

2. Power on the virtual machine

3. After booting, create a user and enable ssh access in the vitual machine then shutdown the VM

4. create a working directory on the host machine

5. Go to the directory and open command line, issue commands

      `$ vagrant package --base=<name of virtual machine within virtual box> --output=<custom name of box>`  
      * Wait for response and run below command  
      
      `$ vagrant box add <custom name of box>.box --name=<custom name>`  
      * Wait for response  
      
      `$ vagrant init`  
      * This will create a file `vagrantfile` in the directory  

6. Use text editor to edit the vagrantfile created by above command in step number (5) to specify username and password  
```
  Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|  
	  # All Vagrant configuration is done here. The most common configuration  
	  # options are documented and commented below. For a complete reference,  
	  # please see the online documentation at vagrantup.com.  
	  # Every Vagrant virtual environment requires a box to build off of.  
	  config.vm.box = "centos64"  
	  config.ssh.username="uaccount"  
	  config.ssh.password="passw0rd"
```

7. The vagrant box is created successfully and to start the machine run the command  
     `` $ vagrant up``

