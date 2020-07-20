# Introduction
You may want to provide elaborate status on tasks being performed by Ansible operator. This helps end user to understand what steps are completed. This also helps devops team to troubleshoot any issues.

Ansible operator by default manage status of CR. You can turned that off by setting manageStatus: false in watches.yaml file.

The ansible tasks in the playbook provides examples of how you can set various status depending on state of your CR