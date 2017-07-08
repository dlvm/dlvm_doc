Tutorial 2 Verify event handling
================================

Prepare::

  $ source /opt/dlvm_env/bin/activate
  $ dlvm dpv create --dpv_name localhost
  $ dlvm dvg create --dvg_name dvg0
  $ dlvm dvg extend --dvg_name dvg0 --dpv_name localhost
  $ dlvm dlv create --dlv_name dlv0 --dvg_name dvg0 --dlv_size 3G --init_size 1G --stripe_number 1
  $ dlvm ihost create --ihost_name localhost
  $ dlvm dlv attach --dlv_name dlv0 --ihost_name localhost

Destroy a leg manually::

  $ leg_id=`dlvm dlv display --dlv_name dlv0 | grep leg_id | tail -n 1 | egrep -o '[0-9,a-f]{32}'`
  $ sudo targetcli /iscsi/ delete iqn.2016-12.dlvm.target.$leg_id

Generate io on dlv (the dd command should spent a long time because a
leg was destroied, but it should success finally::

  $ sudo dd if=/dev/zero of=/dev/mapper/dlvmfront-dlv0 count=1 oflag=direct

Verify the leg failover has been handled properly::

  $ cat /var/log/dlvm/monitor.log | grep handle_event
  2017-07-08 05:54:25,719 - dlvm_monitor_action.py - 79 - INFO - handle_event: ['single_leg_failed', 'dlv0', '0fc8eb51455d49718c5fc0e1dd43c8f7']
  2017-07-08 05:54:51,747 - dlvm_monitor_action.py - 79 - INFO - handle_event: ['fj_mirror_complete', 'a7fbf5249cda4bc19951ab29692860d4']

You should find two events, one is single_leg_failed, which indicate a
leg failed was detected, another is fj_mirror_complete, whcih indicate
the leg failover was completed.

Check the current data size::

  $ dlvm dlv display --dlv_name dlv0 | grep data_size
              "data_size": 1073741824

We can see, although we create a 3G dlv, the actual size it allocated
is 1G.

Write 900M data to dlv0::

  $ sudo dd if=/dev/zero of=/dev/mapper/dlvmfront-dlv0 bs=1M count=900 oflag=direct

Verify the data_size again::

  $ dlvm dlv display --dlv_name dlv0 | grep data_size
              "data_size": 2147483648

We can see, the data_size has been changed to 2G.

Clean environment::

  $ dlvm dlv detach --dlv_name dlv0 --ihost_name localhost
  $ dlvm dlv delete --dlv_name dlv0
  $ dlvm dvg reduce --dvg_name dvg0 --dpv_name localhost
  $ dlvm dvg delete --dvg_name dvg0
  $ dlvm dpv delete --dpv_name localhost
  $ dlvm ihost delete --ihost_name localhost
