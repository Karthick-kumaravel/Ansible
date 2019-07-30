
![](https://image.ibb.co/mckzma/ansible.jpg)

# Ansible Installation and Configuration on Redhat/CentOS 7

**Ansible** is an IT automation tool and it can configure systems, deploy software, and orchestrate more advanced IT tasks such as continuous deployments or zero downtime rolling updates.
Ansible manages machines in an agentless manner. Ansible uses OpenSSH for transport and it is decentralized, that means it relies on your existing OS credentials to control access to remote machines; if needed it can easily connect with
Kerberos, LDAP, and other centralized authentication management systems.How to Install and Configuration of Ansible IT Automation Tool on Linux.

In this step by step guide, you are going to learn how to get started with IT Automation with Ansible. This is your first step to understand the basic functionality of Ansible and how to Install/Configure Ansible IT Automation Tool on Enterprise Linux (RHEL/CentOS 7).  You will also learn how to get Ansible installed with different methods and some basic concepts. you’ll learn how to execute ad-hoc commands from the master node to done some simple tasks across your nodes using Ansible command (/usr/bin/ansible). you’ll also see what sort of modules are available in Ansible’s core (though you can also write your own, which we’ll also show later). So let’s get start with your first question.

![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/Ansible-4.png)

### What is Ansible?

**Ansible** is an open-source automation engine that automates software provisioning, configuration management, and application deployment. Ansible can manage any *NIX platform through the SSH and also Ansible can manage Windows Servers and Network devices.

##### Ansible is the only Automation Engine that can automate the entire application lifecycle and Continues Delivery pipeline.

- **Ansible** is Simple to install and configure and it Can manage almost any *IX through SSH
- Ansible Requires **Python 2.4** for Installation
- Windows (PowerShell, WinRM python module)

### Feature and Benefits of Ansible

**Cross platform support** – can support any *NIX, Windows machines, network devices etc.
**Agentless Architecture**, and minimal setup on Master node. so no work for system admins to manage the agent’s on the client side
**Written in Python**.
**Ansible uses OpenSSH** for *NIX systems and WinRM for communication with MS Windows machines.
**Open source** and activity maintained on the GitHub.
**Very easy install** and configure.
**Highly scalable** – Can manage Thousands of nodes
**Secure as it used SSH** for the communication.
**Easy to understand and remember the command syntax.** (No coding skills needed). Ansible uses simple declarative language YAML

### Ansible is not just automation engine, but it is also used for:

- Configuration Management.
- Application Deployment.
- Orchestrations
- Security and Compliance
- Rolling Upgrades – Continue Deployment (CD)
- Provisioning (AWS, Virtual Platform, OpenStack etc…)

### Key Components of Ansible

- Ansible Modules.
- Inventory.
- Tasks.
- Variables.
- Playbooks
Further to the playbooks, Ansible roles can be configured for the more systematic management of the Ansible playbooks.

### Step 1: Ansible Installation on Red Hat / CentOS 7: 
You can install Ansible control machine with many different methods. In this guide, I am going to show you Ansible installation using Python-PIP and EPEL Repository (YUM).

##### Ansible Master Node details:
Master Node details:-

    Hostname     : ansmaster
    OS           : CentOS 7 x86_64
    IP Address   : 192.168.1.80
    Ansible User : ansadm

##### Ansible Master Node Host file: Remote host details
hosts - file

    [ansadm@ansmaster ~]$ cat /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
 
    ######## Ansible Environment Setup - Master Server info #######
    192.168.1.80    ansmaster.devopsmates.com ansmaster
 
    ####### Ansible Control Nodes - Remote hosts ########
    192.168.1.85    awscli.devopsmates.com  awscli
    192.168.1.84    webapp1.devopsmates.com webapp1
    192.168.1.63    testdb3.devopsmates.com testdb3
    [ansadm@ansmaster ~]$

##### Method -1: Ansible Latest Release Installation Via Pip
Ansible can be installed via “pip”, the Python package manager. If ‘pip’ isn’t already available in your version of
Python, you can get pip install by running the below command.

    [root@ansmaster ~]# easy_install pip
    (or)
    [root@ansmaster ~]# yum install python-pip
    
Ansible also uses the following Python modules that need to be installed:

    [root@ansmaster ~]# pip install paramiko PyYAML jinja2 httplib2
    
After installation of **all pre-requisites and python-pip,** then **install Ansible** with the below command.

**The below command will Ansible latest release from Python Package Manager:**

    [root@ansmaster ~]# pip install ansible
    
##### Method -2: Ansible Latest available Release installation Via Yum

RPMs are available from yum for EPEL 6, 7, and currently supported Fedora distributions. Ansible itself can manage earlier operating systems that contain Python 2.4 or higher. Fedora users can install Ansible directly, though if you are using RHEL or CentOS and have not already done so,
First configure EPEL Repository on Linux, because Ansible packages are available from EPEL. install the epel-release RPM if needed on CentOS, RHEL, or Scientific Linux

    [root@ansmaster ~]# yum install epel-release -y
Now run the below command to install Ansible along with all dependencies.

    [root@ansmaster ~]# yum install ansible -y

### Step 2: Ansible Installation Verification: 
Just executes the ansible –version command to check Ansible available status in your Master node. Checking the Ansible Version and Config Info

    [root@ansmaster ~]# ansible --version
    ansible 2.3.0.0
    config file = /etc/ansible/ansible.cfg
    configured module search path = Default w/o overrides
    python version = 2.7.5 (default, Nov  6 2016, 00:28:07) [GCC 4.8.5 20150623 (Red     Hat 4.8.5-11)]
    [root@ansmaster ~]#
**Ansible Configurations (default):** check the below location for ansible defaults configuration file and inventory file

- Default Config Directory : /etc/ansible
- Default Config File : /etc/ansible/ansible.cfg
- Default Inventory File : /etc/ansible/hosts

Now that you’ve installed Ansible, it’s time to get started with some basics.

### Step 3: Configuring communication between Ansible Master node and target machines

When Ansible communicates with the target machines, by default it assumes the ssh keys are exchanged and password less communication is through. If you want to use the password, can hard-code the password in the inventory file,  but this is not recommended. Also, use ssh-keys for communication.

First, create one common user(ansadm) on both sides to communicate Ansible Master node with remote hosts.

    [root@ansmaster ~]# useradd ansadm
    [root@ansmaster ~]# passwd ansadm
    Changing password for user ansadm.
    New password:
    BAD PASSWORD: The password is shorter than 8 characters
    Retype new password:
    passwd: all authentication tokens updated successfully.
    [root@ansmaster ~]#


Provide full sudo with no password for ansadm user

    [root@ansmaster ~]# echo "ansadm  ALL=(ALL)       NOPASSWD:ALL" >> /etc/sudoers
 
##### Step 1. Generating the Keys on Ansible Master Server (ansmaster):

The first thing,  switch from root user to ansadm user, then create ssh keys

    [root@ansmaster ~]# su - ansadm
Generating ssh keys with 4096 encryption level for ansadm user

    [ansadm@ansmaster ~]$ ssh-keygen -t rsa -b 4096 -C     "ansadm@ansmaster.devopsmates.com"
    
![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/1.jpg)

##### Step 2. Update Keys on Remote Host

Passing ssh keys from Ansible Master Server (ansmaster) to remote hosts,  first time it will ask for the user password

##### Remote Host 1:-
awscli.devopmates.com = 192.168.1.85

    [ansadm@ansmaster ~]$ ssh-copy-id ansadm@awscli.devopsmates.com
    The authenticity of host 'awscli.devopsmates.com (192.168.1.85)' can't be established.
    ECDSA key fingerprint is 83:12:2f:71:a7:19:15:c7:84:87:e7:0a:a9:de:0d:54.
    Are you sure you want to continue connecting (yes/no)? yes
    /bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
    /bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
    ansadm@awscli.devopsmates.com's password:
 
    Number of key(s) added: 1
 
    Now try logging into the machine, with:   "ssh 'ansadm@awscli.devopsmates.com'"
    and check to make sure that only the key(s) you wanted were added.
 
    [ansadm@ansmaster ~]$
##### Remote Host 2 
testdb3.devopsmates.com = 192.168.1.63

    [ansadm@ansmaster ~]$ ssh-copy-id ansadm@testdb3.devopsmates.com

![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/3.jpg)

##### Remote Host 3 
webapp1.devopsmates.com = 192.168.1.84

    [ansadm@ansmaster ~]$ ssh-copy-id ansadm@webapp1.devopsmates.com

![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/2.jpg)

##### Step 3. Testing the Public Key Authentication
Take SSh from Master
    [ansadm@ansmaster ~]$ ssh ansadm@testdb3.devopsmates.com
    [ansadm@ansmaster ~]$ ssh ansadm@webapp1.devopsmates.com
    [ansadm@ansmaster ~]$ ssh ansadm@awscli.devopsmates.com
    
![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/4.jpg)

We’ll assume you are using SSH keys for authentication. To set up SSH agent to avoid retyping passwords, you can do: after transferring the ssh keys, the next basic task is to create inventory for remote hosts. Check out next page to create an inventory file.

### Step 3: INVENTORY
Use the default one **/etc/ansible/hosts** or create a custom host file. This is an inventory file where you have to maintain your remote host’s list. I will discuss more Ansible inventory in the next article.

The things in brackets are group names, which are used in classifying systems and deciding what systems you are controlling at what times and for what purpose.

**My Inventory file (Static).**  The format for **/etc/ansible/hosts** is an INI format and looks like this:
    
    [all]
    awscli.devopsmates.com
    testdb3.devopsmates.com
    webapp1.devopsmates.com
     
    [webservers]
    webapp1.devopsmates.com
 
    [dbservers]
    testdb3.devopsmates.com
 
    [awsmanage]
    awscli.devopsmates.com
    
![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/5.jpg)

### Step 4: Ansible Ad-Hoc COMMANDS:

Introduction To Ad-Hoc Commands Examples of basic commands. An ad-hoc command is something that you might type in to do something really quick, but don’t want to save for later.
This is a good place to start to understand the basics of what Ansible can do prior to learning the playbooks language
– ad-hoc commands can also be used to do quick things that you might not necessarily want to write a full playbook for. Basically, the true power of Ansible is playing with playbooks. I will discuss playbook in the coming articles.

Run your first Ansible Ad-Hoc command to check ping response from remote hosts. The following examples show how to **use /usr/bin/ansible** for running ad hoc tasks.

    [ansadm@ansmaster ~]$ ansible -i /etc/ansible/hosts -m ping all
    
 ![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/6.jpg)   
 
 An Another command to know uptime for your all remote hosts from the Ansible inventory.
 
    [ansadm@ansmaster ~]$ ansible all -i /etc/ansible/hosts -m command -a "uptime"

![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/7.jpg)    

### Step 5: Ansible MODULES

Ansible provides no. of modules that can be executed directly on remote hosts or through playbooks. Modules (also referred to as “task plugins” or “library plugins”) are the ones that do the actual work in ansible, they are what gets executed in each playbook task.
Confused ??? Let’s check all available modules on the Ansible server. You can get your Modules list and the module Documentation by running the below commands. List all modules available with that particular Ansible version
**ansible-doc -l**
Check information of particular module
**ansible-doc <module>**

**LIST ALL MODULES**

    [ansadm@ansmaster ~]$ ansible-doc -l
    
![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/8.jpg)

**VIEW MODULE DOCUMENTATION**

**Syntax :** ansible-doc <module_name>

The below command will show you the document about **“shell module”**. press **“q”** for quit from document page.

    [ansadm@ansmaster ~]$ ansible-doc shell
    
![](http://i0.wp.com/devopsmates.com/wp-content/uploads/2017/04/9.jpg)

### Also, check out the below video for More details:
Please click on the image to watch the video,

[![Watch the video](http://img.youtube.com/vi/tJVkERqw8SI/0.jpg)](https://www.youtube.com/watch?v=tJVkERqw8SI)

### Conclusion:
This is all about Ansible basics. if you’re cleared about the basics, then you are ready to move the next level to play with some basic Ad-Hoc commands on the remote host. I will share more information about Ad-Hoc commands in the next articles. I hope this guide will help you to start your journey towards Automation with Ansible.

