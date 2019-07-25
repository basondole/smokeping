# Getting JunOS on Vagrant

### Pre-requisites  
  - Virtualbox is installed on host machine  
  - Vagrant is installed on host machine  
  - Git is installed on host machine https://git-scm.com/downloads  
  - Existing github account https://github.com/  
  
### Initial setup 
##### 1. Setting up Git  
  - Create a directory for your project from terminal (linux) or cmd (windows)  
     ```
       $ mkdir vagranttest   
       $ cd vagranttest
     ```

        
  - Link your github account  
     ```
       $ git config --global user.name "github_username"  
       $ git config --global user.email "email_address"
     ```

  - Clone below git this will create new directory `vagrant-junos/`  
       `$ git clone https://github.com/JNPRAutomate/vagrant-junos.git`

  - Go to this directory  
       `$ cd vagrant-junos/`  

##### 2. Install Vagrant plugin for junos  
   ```
     $ vagrant plugin install vagrant-junos  
     $ vagrant plugin install vagrant-host-shell
   ```

##### 3. Check the Vagrant machine status
   ```$ vagrant status```  
     
This completes the initial setup. See below image for reference of the above procedures

<img width="566" alt="vagrantjunos" src="https://user-images.githubusercontent.com/50369643/61868108-61036d80-aee1-11e9-9c1e-05fbe4b79310.PNG">

### Downloading the box  
Run the below command to download and start the vagrant box  
   ```$ vagrant up```

   <img width="493" alt="vagrantup" src="https://user-images.githubusercontent.com/50369643/61874283-af6c3880-aef0-11e9-9c08-fd371f1df7a9.PNG">  

Once the box is downloaded it will boot. Verify the status by running the command  
   ```$ vagrant status``` 
   
Test login to the machine  
   ```$ vagrant ssh```  
   
   <img width="493" alt="vagrantssh" src="https://user-images.githubusercontent.com/50369643/61874498-3b7e6000-aef1-11e9-9d71-5f4aa40a0260.PNG">

### Creating topology
The above process has created the base box which runs JunOS 12.1X47-D15.4. In a lab environment you may want to add more and create a topology to work with.  
To add more machines edit the `vagrantfile` created in the `vagrant-junos/ directory`  

Contents of `vagrantfile`  

<img width="478" alt="vagrantfileog" src="https://user-images.githubusercontent.com/50369643/61876465-9dd95f80-aef5-11e9-89ff-08e32e818889.PNG">

#### Sample topology  

<img width="274" alt="vagrant" src="https://user-images.githubusercontent.com/50369643/61876087-b432eb80-aef4-11e9-949d-42430b108ed4.png">

Below is an example of `vagrantfile` that creates the shown topology  

<img width="478" alt="vagrantfile" src="https://user-images.githubusercontent.com/50369643/61875466-594cc480-aef3-11e9-9de5-f4a2941bbf14.PNG">

#### Testing the topology

