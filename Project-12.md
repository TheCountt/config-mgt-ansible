# Ansible Refactoring & Static Assignments (Imports and Roles)

## Step 1 - Refactor Ansible code by importing other playbooks into site.yml

- In Project 11 you wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks 
  and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written 
  there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and your playbook will become messy
  with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

- Within playbooks folder, create a new file and name it site.yml - This file will now be considered as an entry point into the entire infrastructure
  configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed including 
  common.yml that was created previously

- Let see code re-use in action by importing other playbooks. Create a new folder in root of the repository and name it static-assignments. 
  The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept,
  therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.


- Move common.yml file into the newly created static-assignments folder.


- Inside site.yml file, import common.yml playbook.

*---*

*- hosts: all*

*- import_playbook: ../static-assignments/common.yml*


### *The code above uses built in import_playbook Ansible module( Static assignments uses "import_playbook")*

Run ansible-playbook command against the dev environment

*ansible-playbook -i inventory/dev playbooks/site.yml*

## Step 2 - Create and Configure Roles

To create a role, you must create a directory called roles in ansible directory.
There are two ways how you can create this folder structure:

Use an Ansible utility called ansible-galaxy inside ansible/roles directory (you need to create roles directory upfront)

*sudo mkdir roles*

*cd roles*

*ansible-galaxy init webserver*

remove unnecessary directories and files.  The roles structure should look like this after removal

└── webserver
    ├── README.md
    
    
    ├── defaults
    │   └── main.yml
    
    
    ├── handlers
    │   └── main.yml
    
    
    ├── meta
    │   └── main.yml
    
    
    ├── tasks
    │   └── main.yml
    
    
    └── templates
    
In /etc/ansible/ansible.cfg file uncomment "roles_path" string and provide a full path to your roles directory 

*roles_path = /home/ubuntu/ansible/roles*

so Ansible could know where to find configured roles.

It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

- Install and configure Apache

- Clone Tooling website from GitHub https://github.com/TheCountt/tooling.git

- Ensure the tooling website code is deployed to /var/www/html on your Web servers

- Make sure apache service is started
    
    
*NOTE: Do not forget to start the instances we created in Project 11. Also, check inventory/dev to make sure the Private IP addresses and other parameters are correct*


## Step 3 - Reference ‘Webserver’ role

- Within the static-assignments folder, create a new file and name it webservers.yml. This is where you will reference the role.

*---*
*- hosts: uat-webservers*
  *roles:*
     *- webserver*
     
- Remember that the entry point to our ansible configuration is the site.yml file. Therefore, we need to refer ywebservers.yml role inside site.yml. So, we should have this
  in site.yml

---
*- hosts: all*
*- import_playbook: ../static-assignments/common.yml*

*- hosts: webservers*
*- import_playbook: ../static-assignments/uat-webservers.yml*


## Step 4 - Commit & Test

- Commit your changes, create a Pull Request and merge them to main branch in Github.

- Now run the playbook against your dev inventory and see what happens:

*sudo ansible-playbook -i /home/ubuntu/ansible/inventory/dev.yml /home/ubuntu/ansible/playbooks/site.yaml*


