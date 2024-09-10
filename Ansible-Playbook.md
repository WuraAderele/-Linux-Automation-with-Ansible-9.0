# Implementing an Ansible Playbook

The goal of this exercise is to configure and perform adminstrative tasks on managed hosts using a playbook.

## Things to know before getting started

Ansisble is an open-source automation platform that uses simple automation language that can accurately describe an IT application infrastructure. It is used for automating System Admin activities such as configuration management, workflow automation,and network automation.

Ansible has a simple agentless architecture. It is agentless because it does not require agents to be installed on monitored systems to operate. It consists of two main components:

* **Control Node:** A machine where Ansible is installed and where copies of Ansible project files are kept
* **Managed Hosts:** The list of systems that are being managed using Ansible, listed in an inventory file. Ansible connects to managed hosts using SSH or WinRM.

Other components relevant to how Ansible works include:

* **Module:** Small piece of code with specific arguments that can act on managed hosts. Ensures that a particular part of the managed host is in a particular state
* **Task:** A task is a fundamental unit of work that dictates how a module should be executed. Each task runs a module
* **Play:** A play performs a series of tasks in a specified order. It is often expressed in a YAML file.
* **Playbook:** A playbook is a YAML file that contains one or more plays. It provides a way to orchestrate multiple tasks across multiple hosts in a repeated manner

In this exercise, I want to construct and run an Ansible Playbook to install, configure, and verify the status of web and database services on a managed host.

To get started, I would need to create an inventory file. An inventory defines a collection of hosts that Ansible manages. These hosts can also be assigned to groups, which can be managed collectively. Groups can contain child groups, and hosts can be members of multiple groups. The file already exists in this case, so this step will be skipped.

Next, I want to create a new playbook called *internet.yml*. I will add the necessary entries to start a first play named Enable internet services and specify its intended managed host, serverb.lab.example.com. I will also add the necessary entry to enable privilege escalation.

In the correct working directory, run the below command to create the playbook file and open it in a text editor:

          vim internet.yml

To start creating the play as described above, type the following in the internet.yml playbook:

          ---
          -  name: Enable internet services
             hosts: serverb.lab.example.com
             become: true

I want to add the necessary entries to the */home/student/playbook-review/internet.yml* file to define a task that installs the latest versions of the firewalld, httpd, mariadb-server, php, and php-mysqlnd packages. To do this, I will add the below commands to the playbook I already started editing:

          tasks:
          -  name: Install latest version of packages
             ansible.builtin.dnf:
             name:
               - firewalld
               - httpd
               - mariadb-server
               - php
               - php-mysqlnd
             state: latest

I want to add the necessary entries to the /home/student/playbook-review/internet.yml file to define the firewall configuration tasks. The task is intended to ensure that the firewalld service is enabled and running, and that access is allowed to the http service. 

          -  name: firewalld is enabled and running
             ansible.builtin.service:
             name: firewalld
             enabled: true
             state: started

          -  name: firewalld services has access to http service
             ansible.posix.firewalld:
               service: http
               permanent: true
               state: enabled
               immediate: true

I want to add the necessary entries to ensure the httpd and mariadb services are enabled and running.

          -  name: httpd service is enabled and running
             ansible.builtin.service:
             name: httpd
             enabled: true
             state: started

          -  name: mariadb service is enabled and running
             ansible.builtin.service:
             name: mariadb
             enabled: true
             state: started

I want to add the necessary entry that uses the ansible.builtin.copy module to copy the */home/student/playbook-review/index.php* file to the */var/www/html/* directory on the managed host while ensuring the file mode is set to 0644.

          -  name: Test php page is installed
             ansible.builtin.copy:
             src: index.php
             dest: /var/www/html/
             mode: 0644

Next, I want to define another play in the */home/student/playbook-review/internet.yml* file for a task to be performed on the control node. This play tests access to the web server that should be running on the *serverb.lab.example.com* managed host. This play does not require privilege escalation, and runs on the workstation.lab.example.com managed host.

          - name: Test access to web server from control node
            hosts: workstation
            become: false
            tasks:
              -  name: Test internet web service
                 ansible.builtin.uri:
                 url: http://serverb.lab.example.com
                 status_code: 200

The full content of the internet.yml playbook should be:
          ---
          -  name: Enable internet services
             hosts: serverb.lab.example.com
             become: true
             tasks:
              -  name: Install latest version of packages
                 ansible.builtin.dnf:
                 name:
                   - firewalld
                   - httpd
                   - mariadb-server
                   - php
                   - php-mysqlnd
                 state: latest
                 
              -  name: firewalld is enabled and running
                 ansible.builtin.service:
                 name: firewalld
                 enabled: true
                 state: started
    
              -  name: firewalld services has access to http service
                 ansible.posix.firewalld:
                   service: http
                   permanent: true
                   state: enabled
                   immediate: true    

              -  name: httpd service is enabled and running
               ansible.builtin.service:
               name: httpd
               enabled: true
               state: started
  
            -  name: mariadb service is enabled and running
               ansible.builtin.service:
               name: mariadb
               enabled: true
               state: started

            -  name: Test php page is installed
               ansible.builtin.copy:
               src: index.php
               dest: /var/www/html/
               mode: 0644 

          - name: Test access to web server from control node
            hosts: workstation
            become: false
            tasks:
              -  name: Test internet web service
                 ansible.builtin.uri:
                 url: http://serverb.lab.example.com
                 status_code: 200               

With the playbook completed with all the relevants tasks to put the managed hosts in our desired state, we will run the below command to validate the synatx of the internet.yml playbook and ensure there are no errors:

          ansible-navigator run -m stdout internet.yml --syntax-check

After a successful syntax check, we run the below command to execute the playbook:

        ansible-navigator run -m stdout internet.yml

The above command generates output to show whether or not the tasks in the playbook completed successfully.
