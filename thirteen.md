# Ansible Dynamic Assignments (Include) and Community Roles

In this project(which is a continuation of Project 12) we will be introduced to dynamic assignments by using include module.
What is the difference between static and dynamic assignments?

Static assignments use import Ansible module while Dynamic assignments use include Ansible module.

Hence,

import = Static
include = Dynamic

When the import module is used, all statements are pre-processed at the time playbooks are
parsed. Meaning, when you execute site.yml playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the
statements encountered during execution will be used.

Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic ones, it is hard to debug playbook problems due
to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.

In the  GitHub repository we have been usong since Project 11, start a new branch and give it a new name( named mine *thirteen* ).

Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook later.
For now, let us keep building up the structure.

Your GitHub should have the following structure:
