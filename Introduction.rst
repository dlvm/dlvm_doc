Introduction to DLVM
====================

DLVM (Distribute Logical Volume) is a distribute storage system,
intends to be a storage backend for a cloud platform, such as
openstack cinder.

DLVM features:

* REST api interface
* supports create/delete volume, attach/detach volume, create/delete
  snapshot, online clone, increase volume size
* HA (provided by device mapper mirror target)
* thin provision (provided by device mapper thin provision target)
* more than 100K IOPS random write per volume

DLVM has similar concepts as traditional `LVM`_. The traditional
`LVM`_ has pv (physical volume), vg (volume group) and lv (logical
volume), DLVM has the corresponding conecpts which are dpv, dvg and
dlv, the characture 'd' is the abbreviate of 'distribute'. In DLVM, a
dpv is a linux server which can export volume(s) through iscsi. A dvg
is a group of dpvs, a dlv is a logical aggregation of dpvs in a same
dvg. A dlv can be attached to an ihost(initiator host) through iscsi
protocol.
Below is the architecture of the dlvm service:

.. figure:: image/architecture.png
   :scale: 50%

There are 6 components of the dlvm services:

* API Server
* DB
* dpv
* ihost
* Message Queue
* Monitor

.. _LVM: https://en.wikipedia.org/wiki/Logical_Volume_Manager_%28Linux%29
