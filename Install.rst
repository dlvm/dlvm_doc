Install DLVM
============

Currently, dlvm can only work on ubuntu 16.04 system. It can be
deployed by an ansible playbook. Download the playbook from here:
https://github.com/dlvm/dlvm_playbook . 

========================
prepare: install ansible
========================

Login to a ubuntu 16.04 system, install ansible::

  $ sudo apt-get install -y software-properties-common
  $ sudo apt-add-repository -y ppa:ansible/ansible
  $ sudo apt-get update
  $ sudo apt-get install -y ansible


=====================
all in one deployment
=====================

Download dlvm_playbook and exact it::

  $ curl -O -L https://github.com/dlvm/dlvm_playbook/archive/master.zip
  $ unzip master.zip

Go to the dlvm_playbook directory, prepare the ssh login credentials.
and configure ssh login credentail depend on the ubuntu system.

Rename the group_vars/all.sample to group_vars/all, and edit it, set
test_mode to true, set dlvm_local_lvm and dlvm_all_in_one to true

Run dlvm_playbook::

  $ ansible-playbook -i "localhost," --user <user_name> -k --become --become-user root control_plan.yml
  $ ansible-playbook -i "localhost," --user <user_name> -k --become --become-user root -e "dlvm_local_lvm=true" dpv.yml
  $ ansible-playbook -i "localhost," --user <user_name> -k --become --become-user root ihost.yml

If your ssh use keypair to login, please run::

  $ ansible-playbook -i "localhost," --user <user_name> --private-key <key_path> --become --become-user root control_plan.yml
  $ ansible-playbook -i "localhost," --user <user_name> --private-key <key_path> --become --become-user root -e "dlvm_local_lvm=true" dpv.yml
  $ ansible-playbook -i "localhost," --user <user_name> --private-key <key_path> --become --become-user root ihost.yml

The control_plan.yml will deploy api_server, worker, monitor to
the server, and for easy to use, it also deploys a single node rabbitmq
and a single node mariadb on localhost.
The dpv.yml will deploy dlvm_dpv_agent to the server, set
dlvm_local_lvm to true will let it create a local volume group from a
loop device, it could be used in test mode, in production, you should
configure your own logical volume.
The ihost.yml will deploy dlvm_ihost_agent to the server.

By default, the api server is listen on localhost:9521, you could run
below command to verify it works::

  $ curl localhost:9521/
  {"data": "dlvm", "req_id": "3662c3b1-88e0-48a3-b4b5-03ec9f5248fe", "message": "succeed"}

Create a dpv::

  $ curl -H "Content-Type: application/json" -X POST -d '{"dpv_name":"localhost"}' http://localhost:9521/dpvs

Create a dvg::

  $ curl -H "Content-Type: application/json" -X POST -d '{"dvg_name":"dvg0"}' http://localhost:9521/dvgs

Add the dpv to the dvg::

  $ curl -H "Content-Type: application/json" -X PUT -d '{"dpv_name":"localhost"}' http://localhost:9521/dvgs/dvg0/extend

Create a dlv::

  $ curl -H "Content-Type: application/json" -X POST -d '{"dlv_name":"dlv0","dlv_size":1073741824,"stripe_number":1,"init_size":536870912,"dvg_name":"dvg0"}' http://localhost:9521/dlvs

Wait for a while, check the status of dlv0, make sure it is 'available'::

  $ curl http://localhost:9521/dlvs

Attache the dlv0 to an ihost (localhost in our example)::

  $ curl -H "Content-Type: application/json" -X PUT -d '{"ihost_name":"localhost"}' http://localhost:9521/dlvs/dlv0/attach

Wait for a while, check the status of dlv0, make sure it is
'attached', and then you could find /dev/mapper/dlvmihost-final-dlv0
on localhost::

  $ ls /dev/mapper/dlvmihost-final-dlv0
  /dev/mapper/dlvmihost-final-dlv0

You could use it as a normal block device.

Detache the dlv from ihost::

  $ curl -H "Content-Type: application/json" -X PUT http://localhost:9521/dlvs/dlv0/detach

Delete the dlv::

  $ curl -X DELETE http://localhost:9521/dlvs/dlv0

Remove dpv from dvg::

  $ curl -H "Content-Type: application/json" -X PUT -d '{"dpv_name":"localhost"}' http://localhost:9521/dvgs/dvg0/reduce


Delete dpg and dpv::

  $ curl -X DELETE http://localhost:9521/dvgs/dvg0
  $ curl -X DELETE http://localhost:9521/dpvs/localhost
