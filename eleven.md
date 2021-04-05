
# Ansible Configuration Management - Automate Project 7 to 10

## Step 1 - Create a new GitHub repository named config-mgt-ansible


## Step 2 - Prepare a Jump or Bastion server to act as Ansible Client

Create a VM and call it Bastion. It will serve as a client to run ansible scripts.

On the bastion host, create a folder and name it ansible

sudo mkdir ansible

### Install Ansible

sudo apt install software-properties-common -y
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y



## Step 3 - Prepare Visual Studio Code for Development(Working with VS Code)
We have to check first if we can connect to the remote host(the newly spinned-up VM) from windows. To connect to remote AWS EC2 instance(Linux servers) from windows(Powershell, windows cmd). Take the following steps:

Go to settings in the PC, and go to optional features, check if Open SSH Server and Open SSH Client are already installed. If not, click on the 'Add optional features'  find and install open SSH Server and also Open SSH Client.

You can also install on PowerShell. Check the internet on how to go about this.

After installing both Open SSH Server and Open SSH Client, we need to follow these steps:

Save the SSH key(the AWS Key Pair) into the Local disk(C:) on PC

C:\Users\Remi\.ssh\AWS KeyPair.pem

- Change directory to where the ssh key is in PowerShell
 
 cd C:\Users\Remi\.ssh\

- Commands to run on PowerShell to change the permission of SSH Key

$path="./AWS KeyPair.pem"

  #### #reset to remove explicit permissions
 
 icacls.exe $path /reset

  #### #give current user explicit read-permission

icacls.exe $path /GRANT:R "$($env:USERNAME):(R)"

  #### #disable Inheritance and remove inherited permissions
  
  icacls.exe $path /inheritance:r



- To Establish Connection to EC2 Instance from PowerShell
 
 ssh -i "AWS KeyPair" user@hostname


If connection is established, then we can connect Visual Studio Code(VSCode) to the remote host. Take the following steps:

- Donwload and install git on your local system(PC, desktop computer,etc).
- Download and install VScode on your system and check the settings to make sure git is enabled
- In VSCode, go to extensions, search for Remote Development Extension and install
- After installation of the extension, click on 'Terminal' menu at the top and choose 'New Terminal'. A Terminal interface will open at the bottom so as to work on the server.
- If after clicking on the 'new terminal', a bash prompt does not appear at the bottom, at the the right side, you'll see a '+' sign, click on it and choose bash fro the option provided.

To enable Visual Studio Code edit files of remote host,change the ownership of ansible directory in remote host

sudo chown -R ubuntu: ansible




## Step 4- Commence Ansible development

Go to github. Within the prior git repository we created, create a branch and name it


In the newly created branch, create a directory and name it playbooks. This will be used to store all your playbook files.

Create another directory and name it inventory. This will be used to keep your hosts organised.

Within the newly created playbooks directory(folder), create your first playbook, and name it common.yml

Within the newly created inventory folder, create an inventory file for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.



### Setting up the Ansible Inventory

Go to the Bastion/Jumper Server and change directory into ansible

cd ansible

Create a directory named playbooks. This will be used to store all the playbook files:

sudo mkdir ansible/playbooks

Create a directory named inventory. This will be used to keep the host servers organized:

sudo mkdir ansible/inventory

Within the playbooks folder, create a playbook, and name it common.yml

sudo touch ansible/playbooks/common.yml

Within the inventory folder, create an inventory file for each environment (Development, Staging, Testing and Production) dev, staging, uat, and prod respectively.

sudo touch ansible/inventory/dev

sudo touch ansible/inventory/staging

sudo touch ansible/inventory/uat

sudo touch ansible/inventory/prod

Open the ansible/inventory/dev file with an editor

sudo vi ansible/inventory/dev


Input the inventory structure file to start configuring the development servers with your IP addresses.


If a server has a different username and a different SSH private key from the id_rsa key(You should already have saved the SSH key into a created file- like id_rsa key being referred to here - in the /home/ubuntu/.ssh directory)in the Bastion server, the IP entry for that server will look like:

<private-ip-address> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

Change the file ownership and permission of the private key(s) to be used to connect to the servers from the Ansible host to allow for connection and also limit access for security purposes:

sudo chown -R ubuntu: id_rsa

sudo chmod 400  ~/.ssh/id_rsa

The Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

### Common Playbook
It is time to start giving Ansible the instructions on what you want to get done, and how you want it.

Within the common.yml playbook created in our Bastion Server, you will write configuration for repeatable , re-usable, and multi-machine tasks that are common to systems within the infrastructure. Therefore this section will run on all hosts.

Using Project 7 as a guide, write tasks using appropriate Ansible modules that will deploy common configuration to every server. For example,

– Updating the yum repository
 – Installing common software packages like Apache, nfs-server,etc
– Creating directory that must exist on every server
 – Setting timezone to the company’s desired geographic location, etc.

Open the Ansible playbook common.yml with a text editor and write your ansible script. Save and close when done.

sudo vi ansible/playbooks/common.yml


Too see the ansible script I wrote and saved, please check my github account repository

Before proceeding further, test reachability to the servers written in the inventory/dev file with a 'ping' test that returns 'pong' if successful:

ansible all -i ansible/inventory/dev -m ping


## Step 5 - Test Ansible with a Playbook

Now, it is time to execute Ansible and verify if your playbook actually works

ansible-playbook -i inventory/dev  playbooks/common.yml


## Step 6 - Update GIT with the latest code

Since Git is already installed in the Bastion Server, change directory into the ansible directory and start a local git repository:

cd ansible 

sudo git init


Add the inventory and playbook directories to the repository

sudo git add inventory

sudo git add playbooks


Commit the added repositories

sudo git commit -m "first commit"

Rename the "master" branch in the local Git repository to "main":

sudo git branch -m main

Create a new branch Ansible/Project-11 (same name with the branch created in the GitHub repository) and checkout into it:

sudo git checkout -b Ansible/Project-11

Files can be created in the ansible directory and changes can be made to files in inventory and playbooks directories. To check for changes to add and commit in the current branch (Ansible/Project-11), run:

sudo git status

If there are files in red colour, they are yet to be added to the branch. If there are files in green colour, they are yet to be committed. Add the necessary files/folders and commit them.

sudo git add <file>


sudo git commit -m "add comment here"

Confirm if there is anything left to add and commit:

sudo git status


To view the files in the branch:

git ls-tree -r --name-only Ansible/Project-11


Add a remote repository (in this case, the repository created in GitHub):

sudo git remote add origin https://github.com/TheCountt/ansible-config-mgt.git



To pull from GitHub:

git pull <remote> <branch>



Push the local Ansible/Project-11 branch to the remote Ansible/Project-11 branch:

sudo git push -u origin Ansible/Project-11


Create a Pull Request. Pull requests let you tell others about changes you've pushed to a branch in a repository on GitHub. Once a pull request is opened, you can discuss and review the potential changes with collaborators and add follow-up commits before your changes are merged into the base (main) branch.

After successfully pushing to the GitHub Ansible/Project-11 branch, create a pull request to merge the Ansible/Project-11 branch to the main branch:

You may add a comment to the pull request before creating it.

Act as a reviewer and review the pull request of the new feature development.


Merge the code to the main branch and close the pull request:



Back to the Bastion server, switch to the local main branch:

sudo git checkout main

Pull the remote main branch into the local main branch

sudo git pull https://github.com/TheCountt/ansible-config-mgt main


To view the files in the branch:

sudo git ls-tree -r --name-only main



## Credits

https://professional-pbl.darey.io/en/latest/project11.html?next=https%3A%2F%2Fprofessional-pbl.darey.io%2Fen%2Flatest%2Fproject11.html&ticket=ST-1616164414-KTMU4o2z2eGeYDCV0NqhGeLNUjVKJ8GT

docs.ansible.com

https://github.com/ansible/ansible

https://zepel.io/blog/how-to-merge-branches-in-github


