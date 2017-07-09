Tutorial 3 Error handling
=========================

Prepare::

  $ source /opt/dlvm_env/bin/activate
  $ dlvm dpv create --dpv_name localhost
  $ dlvm dvg create --dvg_name dvg0
  $ dlvm dvg extend --dvg_name dvg0 --dpv_name localhost
  $ dlvm dlv create --dlv_name dlv0 --dvg_name dvg0 --dlv_size 3G --init_size 1G --stripe_number 1

Create an invalid ihost::

  $ dlvm ihost create --ihost_name invalid_ihost

Attach the dlv to the invalid ihost::

  $ dlvm dlv attach --dlv_name dlv0 --ihost_name invalid_ihost


From the error message, we can find something like::

  dlvm.utils.error.FsmFailed: {'obt': {'t_id': 'ab4acbc3282645a0a11095d9ff47a9e8', 't_stage': 2, 't_owne
  r': '7a4613cf382747dab8cc3af0ad3323ca'}

Because the invalid_ihost is not contactable, dlvm can not complete
the api request or withdraw from invalid_ihost. We should set it
status to unavailabe, then dlvm will skip the rpc related with invalid_ihost::

  $ dlvm ihost unavailable --ihost_name invalid_ihost

Then resume the obt (The dlv0 will be roll back to 'detached' status)::

  $ dlvm obt resume --t_id ab4acbc3282645a0a11095d9ff47a9e8

For more infomation about obt, please refer to :doc:`obt`

Clean environment::

  $ dlvm ihost delete --ihost_name invalid_ihost
  $ dlvm dlv delete --dlv_name dlv0
  $ dlvm dvg reduce --dvg_name dvg0 --dpv_name localhost
  $ dlvm dvg delete --dvg_name dvg0
  $ dlvm dpv delete --dpv_name localhost
