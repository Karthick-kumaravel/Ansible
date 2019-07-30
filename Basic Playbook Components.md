
![](https://image.ibb.co/ccDMjv/ansible.jpg)

## Here is some of the Basic Playbook Components required for our first playbook:

### Hosts and Users
For each play in a playbook, you get to choose which machines in your infrastructure to target and what remote user to complete the steps (called tasks) as.

The **hosts** line is a list of one or more groups or host our ansilbe server can execute the respective tasks,
The **remote_user** is just the name of the user account in the remote server,

    - hosts: A
      remote_user: root

Remote users can also be defined per task:
    
    - hosts: A
      remote_user: root
      tasks:
           - name: test connection
             ping:
             remote_user: yourname

##### Task 
A task  is simply the use of one of Ansible modules. Module implements specific functionality.  For example, installing package would be task since it will require us to use the ‘yum’ module. There many modules, so task can be running service, fetching files, adding user and many more modules waiting for you to explore.

Let’s see how task looks like:

    - name: Ensure python-ryu is installed
      yum: name=python-ryu state=present
      
In the above example we use yum module to install package named ‘python-ryu’. The state is the action we are using on this package. so ‘present’ tells Ansible to make sure python-ryu installed in the system. There are additional states, as ‘latest’ which means ‘make sure latest package is installed’, so if you have python-ryu-1.0 installed in your system, but there is python-ryu-2.0 available, it will be updated. This is not the case for state=present, which simply cares on whether package is installed or not.

**TIP:** to see what states and other options available for yum module, use this command: **ansible-doc yum**

##### Handlers

Sometimes when you make a configuration change it's necessary to restart the service. Ansible supports this though "handlers".

Imagine that whenever the index.html page changes you need to restart apache (although that's not actually true). You add a "notify:" statement to every action which needs the restart, and a "handler:" which performs the restart.

    tasks:
    - name: install web server
      apt: pkg=apache2 state=present
    - name: install index page
      copy: src=front.html dest=/var/www/html/index.html backup=yes
      notify: restart apache2
    handlers:
    - name: restart apache2
      service: name=apache2 state=restarted
      
Run your playbook again, firstly without changing front.html, and then after changing front.html.

In the latter case you should see

    NOTIFIED: [restart apache2]

which shows that the handler was triggered.

##### Tags

As your playbook gets bigger, it may get slower to run, and you may wish to run only part of a playbook to bypass the earlier steps. The way to do this is using 'tags'. 

    tasks:
    - name: install web server
      apt: pkg=apache2 state=present
      tags: install
    - name: install index page
      copy: src=front.html dest=/var/www/html/index.html backup=yes
      tags: configure
      notify: restart apache2
    handlers:
    - name: restart apache2
      service: name=apache2 state=restarted
      
Now try:

    ansible-playbook web.yml -t configure
    
and it will run only the task which has been tagged as "configure". When writing a playbook, you can assign whatever tags make sense to you.

##### Play

Play is a collection of tasks running against one or more hosts. It includes one or more task.

    - hosts:
    - host A
    - host B
    tasks:  
    - name: install web server
      apt: pkg=apache2 state=present
      tags: install
    - name: install index page
      copy: src=front.html dest=/var/www/html/index.html backup=yes
      tags: configure
      notify: restart apache2
    handlers:
    - name: restart apache2
      service: name=apache2 state=restarted

    - hosts:
    - host A
     tasks:
    - name: install mysql server
      apt: pkg=mysql-server state=present

##### Playbook

Playbook composed of one or more plays.

It’s important to be familiar with the relation between task, play and and playbook,Please find the below:

![](https://image.ibb.co/bWRASF/ansible_1.jpg)

##### Executing A Playbook

Now that you’ve learned playbook syntax, how do you run a playbook? It’s simple. Let’s run a playbook as below,

    ansible-playbook playbook.yml

##### Conclusion
Hope this is bit enough to write the **first simple playbook on ansible.**. Let try your own first playbook, GOOD LUCK 

