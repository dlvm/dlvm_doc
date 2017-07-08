Tutorial 1 Basic usage
====================

Come into the dlvm virtualenv, by default, it is in /opt/dlvm_env::

  $ source /opt/dlvm_env/bin/activate

Create dpv::

  $ dlvm dpv create --dpv_name localhost

Create dvg::

  $ dlvm dvg create --dvg_name dvg0

Extend dvg::

  $ dlvm dvg extend --dvg_name dvg0 --dpv_name localhost

Create dlv::
  
  $ dlvm dlv create --dlv_name dlv0 --dvg_name dvg0 --dlv_size 3G --init_size 1G --stripe_number 1

Create ihost::

  $ dlvm ihost create --ihost_name localhost

Attach dlv to ihost::

  $ dlvm dlv attach --dlv_name dlv0 --ihost_name localhost

Now we can use /dev/mapper/dlvmfront-dlv0 on localhost::

  $ sudo mkfs.ext4 /dev/mapper/dlvmfront-dlv0
  $ sudo mount /dev/mapper/dlvmfront-dlv0 /mnt
  $ sudo touch /mnt/foo
  $ sudo sync

Create a snapshot::
  
  $ dlvm snap create --dlv_name dlv0 --snap_name snap1 --ori_snap_name base

Continue do something on dlv, then umount dlv and detach dlv from ihost::

  $ sudo touch /mnt/bar
  $ sudo umount /mnt
  $ dlvm dlv detach --dlv_name dlv0 --ihost_name localhost

Change the active sanpshot to snap1 and delete the base snapshot::

  $ dlvm dlv set_snap --dlv_name dlv0 --snap_name snap1
  $ dlvm snap delete --dlv_name dlv0 --snap_name base

Attach dlv to ihost again::

  $ dlvm dlv attach --dlv_name dlv0 --ihost_name localhost

Verify the data in dlv0 is the same as the time when we create the snapshot::

  $ sudo mount /dev/mapper/dlvmfront-dlv0 /mnt
  $ ls /mnt

Umount dlv and detach it::

  $ sudo umount /mnt
  $ dlvm dlv detach --dlv_name dlv0 --ihost_name localhost

Delete dlv::

  $ dlvm dlv delete --dlv_name dlv0

Remove dpv from dvg, delete dpv/dvg/ihost::

  $ dlvm dvg reduce --dvg_name dvg0 --dpv_name localhost
  $ dlvm dvg delete --dvg_name dvg0
  $ dlvm dpv delete --dpv_name localhost
  $ dlvm ihost delete --ihost_name localhost
