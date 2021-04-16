# Ansible Refactoring & Static Assignments (Imports and Roles)

## Step 1 - Refactor Ansible code by importing other playbooks into site.yml

- In Project 11 you wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks 
  and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written 
  there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and your playbook will become messy
  with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

- Within playbooks folder, create a new file and name it site.yml - This file will now be considered as an entry point into the entire infrastructure
  configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed including 
  common.yml that was created previously
  
  

![Screenshot 2021-03-29 211512](https://user-images.githubusercontent.com/76074379/114617731-5eb8e800-9c5d-11eb-9c5b-4b0d3781b123.png)



- Let see code re-use in action by importing other playbooks. Create a new folder in root of the repository and name it static-assignments. 
  The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept,
  therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.
  
  
  ![Screenshot 2021-03-29 210529](https://user-images.githubusercontent.com/76074379/114618450-35e52280-9c5e-11eb-9c08-de32b33f28f1.png)



- Move common.yml file into the newly created static-assignments folder.



![Screenshot 2021-03-29 212510](https://user-images.githubusercontent.com/76074379/114619365-5497e900-9c5f-11eb-8391-b8606dccb211.png)



- Inside site.yml file, import common.yml playbook.

*---*

*- hosts: all*

*- import_playbook: ../static-assignments/common.yml*


### *The code above uses built in import_playbook Ansible module( Static assignments uses "import_playbook")*

Run ansible-playbook command against the dev environment

*ansible-playbook -i inventory/dev playbooks/site.yml*


![Screenshot 2021-03-29 212604](https://user-images.githubusercontent.com/76074379/114619542-8ad56880-9c5f-11eb-964f-1e1ae65e40ca.png)




## Step 2 - Create and Configure Roles

To create a role, you must create a directory called roles in ansible directory.
There are two ways how you can create this folder structure:

Use an Ansible utility called ansible-galaxy inside ansible/roles directory (you need to create roles directory upfront)

*sudo mkdir roles*

![Screenshot 2021-03-29 212733](https://user-images.githubusercontent.com/76074379/114619727-b6f0e980-9c5f-11eb-86ab-8719c9c39abf.png)



*cd roles*


![Screenshot 2021-03-29 212843](https://user-images.githubusercontent.com/76074379/114619952-ea337880-9c5f-11eb-8e11-072b8d6a3042.png)



*ansible-galaxy init webserver*

![Screenshot 2021-03-29 213024](https://user-images.githubusercontent.com/76074379/114620228-3979a900-9c60-11eb-8748-a2da9f579aba.png)


Remove unnecessary directories and files.  The roles structure should look like this after removal

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
    
    ![{723B55DA-1DE7-4E1D-997B-7FAAA1536AF0} png](https://user-images.githubusercontent.com/76074379/114937909-467ad180-9df3-11eb-90e2-e5013312afbe.jpg)
    
    
    
*NOTE: Do not forget to start the instances we created in Project 11. Also, check inventory/dev to make sure the Private IP addresses and other parameters are correct*


## Step 3 - Reference ‘Webserver’ role

- Within the static-assignments folder, create a new file and name it webservers.yml. This is where you will reference the role.

---

*- hosts: webservers*
  *roles:*
     *- webserver*
     
   ![{3715D34C-005D-4FA1-A547-B144809FF93A} png](https://user-images.githubusercontent.com/76074379/114937997-63afa000-9df3-11eb-9a84-cff7c465886d.jpg)
     
     
     
- Remember that the entry point to our ansible configuration is the site.yml file. Therefore, we need to refer ywebservers.yml role inside site.yml. So, we should have this
  in site.yml

---
*- hosts: all*
*- import_playbook: ../static-assignments/common.yml*

*- hosts: webservers*
*- import_playbook: ../static-assignments/webservers.yml*


![{F2D1C8E6-DF72-49C6-90B3-DC24B896FDCF} png](https://user-images.githubusercontent.com/76074379/114938177-a5d8e180-9df3-11eb-9352-3b5a9b0e3456.jpg)


## Step 4 - Commit & Test

- Commit your changes, create a Pull Request and merge them to main branch in Github.

- Now run the playbook against your dev inventory and see what happens:

*sudo ansible-playbook -i /home/ubuntu/ansible/inventory/dev.yml /home/ubuntu/ansible/playbooks/site.yaml*


![{B5F83C50-3934-45B4-86CF-2E75594862C6} png](https://user-images.githubusercontent.com/76074379/114938413-f0f2f480-9df3-11eb-8aa9-1f71f08fd035.jpg)



## Credit

https://professional-pbl.darey.io/en/latest/project12.html





