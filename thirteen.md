# Ansible Dynamic Assignments (Include) and Community Roles

## Hint: Always add and commit every little change to Github to avoid Merge Hell. Once you are done and satisfied with your code you can push to Github and create a pull request to merge with the main branch.

## *Please check ansible-config-mgt repository to view code*

In this project(which is a continuation of Project 12) we will be introduced to dynamic assignments by using include module.

What is the difference between static and dynamic assignments?

Static assignments use import Ansible module while Dynamic assignments use include Ansible module.

Hence,

import = Static

include = Dynamic

When the import module is used, all statements are pre-processed at the time playbooks are parsed. This means that when you execute site.yml playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the
statements encountered during execution will be used.

Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic ones, it is hard to debug playbook problems due
to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.

In the  GitHub repository we have been using since Project 11, start a new branch and give it a new name( named mine *thirteen* ).

Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it *env-vars.yml*. We will instruct *site.yml* to include this playbook later. For now, let us keep building up the structure.

Your GitHub should have the following structure:

├── dynamic-assignments

    └── env-vars.yml

├── inventory

    └── dev
    
    └── stage
    
    └── uat
    
    └── prod
    
└── playbooks

    └── site.yml
└── roles (optional folder)

    └──...(optional subfolders & files)
    
└── static-assignments

    └── common.yml
    
    
Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder *env-vars*, then for each environment, create new YAML files which we will use to set variables.

Your layout should now look like this.

├── dynamic-assignments

    └── env-vars.yml
   
├── env-vars

    └── dev.yml
    
    └── stage.yml
    
    └── uat.yml
    
    └── prod.yml
    
├── inventory

    └── dev
    
    └── stage
    
    └── uat
    
    └── prod
    
├── playbooks

    └── site.yml
    
└── static-assignments

    └── common.yml
    
    └── webservers.yml

Now paste the instruction below into the *env-vars.yml* file.

*---*

*- name: collate variables from env specific file, if it exists*

  *hosts: all*

 *tasks:*
 
   *- name: looping through list of available files*
   
     include_vars: "{{ item }}"
     
      with_first_found:
      
         - files:
        
            - uat.yml
            
            - prod.yml
            
            - stage.yml
            
            - dev.yml
            
          paths:
          
            - "{{ playbook_dir }}/../env-vars"
           
      tags:
      
         - always


![{F2FA9692-264D-402A-94EE-6636C1C90EFE} png](https://user-images.githubusercontent.com/76074379/115360422-753ad400-a174-11eb-9794-3647164985a0.jpg)


Notice 3 things to notice here:

We used *include_vars* syntax instead of *include*, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the 

include module is deprecated and variants of *include_* must be used. These are:

- include_role

- include_tasks

- include_vars


In the same version, variants of import were also introduced, such as:

- import_role

- import_tasks


We made use of a special variables *{{ playbook_dir }}* and *{{ inventory_file }}*. *{{ playbook_dir }}* will help  Ansible to determine the location of the running playbook, 

and from there navigate to other path on the filesystem. *{{ inventory_file }}* on the other hand will dynamically resolve to the name of the inventory file being used, then

append *.yml* so that it picks up the required file within the *env-vars* folder.


We are including the variables using a loop. *with_first_found* implies that, looping through the list of files, the first one found is used. This is good so that we can always 

set default values in case an environment specific env file does not exist.

Update *site.yml* with dynamic assignments


*site.yml* should now look like this.

*---*

 *- name: Include dynamic variables*

   *hosts: all*

   *tasks:*
  
      - import_playbook: ../static-assignments/common.yml
    
      - include_playbook: ../dynamic-assignments/env-vars.yml

  *tags:*
  
      - always

 *- name: Webserver assignment*

   *hosts: webservers*

  *import_playbook: ../static-assignments/webservers.yml*
  

## Community Roles

Now it is time to create a role for MySQL database - it should install the MySQL package, create a database and configure users. There are tons of roles that have already been 

developed by other open source engineers out there. These roles are actually production ready, and dynamic to accomodate most of Linux flavours. With Ansible Galaxy, we can 

simply download a ready to use ansible role, and keep going.


## Download Mysql Ansible Role

You can browse available community roles at *galaxy.ansible.com* We will be using a MySQL role developed by *geerlingguy*.


Go to *config-mgt-ansible/roles* directory and run command:

*ansible-galaxy install geerlingguy.mysql*

Change the name of role to *mysql*

![{97EDC324-445B-49CF-B755-45A24D093D6E} png](https://user-images.githubusercontent.com/76074379/115364023-d1ebbe00-a177-11eb-97d5-bb832b1b3179.jpg)


Read the  README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.


## Load Balancer Roles

We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

- Nginx

- Apache


Now, we can either develop your own roles, or find available ones from the community(galaxy.ansible.com) and follow the instructions in their respective README.md files

Update both *static-assignments* and *site.yml* files to refer the roles


![{05FBA126-D888-4B78-A24C-454E29C67807} png](https://user-images.githubusercontent.com/76074379/115361365-5d178480-a175-11eb-9bb6-4ee7264b33e8.jpg)


![{68E9DAD2-DF85-4836-B02C-671929CCC6F3} png](https://user-images.githubusercontent.com/76074379/115361096-188be900-a175-11eb-8e42-d2b0ec89bd64.jpg)


## Important Hints:

Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one - this is where you can make use of variables. Declare a variable in 

*defaults/main.yml* file inside the Nginx and Apache roles. Name each variables *enable_nginx_lb* and *enable_apache_lb* respectively. Set both values to false like this 

*enable_nginx_lb: false* and *enable_apache_lb: false*. Declare another variable in both roles *load_balancer_is_required* and set its value to false as well

For Nginx LB

enable_nginx_lb: false

load_balancer_is_required: false


![{948DE440-2C7A-4E90-9AC6-90FFF63DA5CE} png](https://user-images.githubusercontent.com/76074379/115361519-820bf780-a175-11eb-9165-ea5d644eb535.jpg)


For Apache LB

enable_apache_lb: false

load_balancer_is_required: false


![{59CC360E-5EF2-434F-83EB-6E8CE15976E4} png](https://user-images.githubusercontent.com/76074379/115361824-ce573780-a175-11eb-83fa-742755032309.jpg)


Update both *static-assignments* and *site.yml* files respectively


*lb.yml* file

 *- hosts: lb*

   *roles:*
  
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }


![{05FBA126-D888-4B78-A24C-454E29C67807} png](https://user-images.githubusercontent.com/76074379/115361365-5d178480-a175-11eb-9bb6-4ee7264b33e8.jpg)

*site.yml* file

   *- name: Loadbalancers assignment*
   
     hosts: lb
     
     import_playbook: ../static-assignments/lb.yml
     
     when: load_balancer_is_required
        
        
![{68E9DAD2-DF85-4836-B02C-671929CCC6F3} png](https://user-images.githubusercontent.com/76074379/115361096-188be900-a175-11eb-8e42-d2b0ec89bd64.jpg)


Now you can make use of *env-vars\uat.yml* file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

enable_nginx_lb: true

load_balancer_is_required: true


![{48D15B24-0CE2-42D9-B058-288F6889183D} png](https://user-images.githubusercontent.com/76074379/115362278-36a61900-a176-11eb-9725-7d806d9d4649.jpg)



To test this, change the directory to *config-mgt-ansible*, you can update inventory for each environment and run Ansible against each environment.

*cd confiig-mgt-ansible*

*ansible-playbooks -i inventory/uat playybooks/site.yml*


![{67C1A259-C7D4-4F1C-8A7B-0D9C393BAFF3} png](https://user-images.githubusercontent.com/76074379/115362651-9a304680-a176-11eb-9e22-b2b7327a6e58.jpg)


*Note: The skipped parts of the play is for apache LB which is not enabled and needless configuration of MySQL*

The same must work with apache LB, so you can switch it by setting respective environmental variable to true and nginx to false.

enable_apache_lb: true

load_balancer_is_required: true


To test this, change the directory to *config-mgt-ansible*, you can update inventory for each environment and run Ansible against each environment.

*cd config-mgt-ansible*

*ansible-playbooks -i inventory/uat playybooks/site.yml*

![{5280150B-FCC4-45D6-856E-B9D8632CD7F0} png](https://user-images.githubusercontent.com/76074379/115363043-f4310c00-a176-11eb-85d0-c6d29971138e.jpg)


*Note: The skipped parts of the play is for nginx LB which is not enabled and needless configuration of MySQL*


## Credit

https://professional-pbl.darey.io/en/latest/project13.html

https://galaxy.ansible.com/nginxinc/nginx

https://galaxy.ansible.com/apache
