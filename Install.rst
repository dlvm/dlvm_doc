Install DLVM
============

Currently, dlvm can only work on ubuntu 16.04 system. It can be
deployed by an ansible playbook. Download the playbook from here:
https://github.com/dlvm/dlvm_playbook . 

========
install dlvm in all-in-one mode
========
Login to a ubuntu 16.04 system, install ansible::

  $ sudo apt-get install software-properties-common
  $ sudo apt-add-repository ppa:ansible/ansible
  $ sudo apt-get update
  $ sudo apt-get install ansible

Download dlvm_playbook and exact it::

  $ curl -O -L https://github.com/dlvm/dlvm_playbook/archive/master.zip
  $ unzip master.zip

Go to the dlvm_playbook directory, rename the hosts.sample to hosts,
and configure ssh login credentail depend on the ubuntu system.

Rename the group_vars/all.sample to group_vars/all, and edit it, set
cross_dpv to false, set dlvm_local_lvm and dlvm_all_in_one to true

Run dlvm_playbook::

  $ ansible-playbook -i hosts site.yml
