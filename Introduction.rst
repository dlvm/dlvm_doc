Introduction to DLVM
====================

DLVM (Distribute Logical Volume) is a distribute storage system,
intends to be a storage backend for a cloud platform. It has a REST
API interface, which supports create or delete volume, attach a volume
to a host, detach a volume from a host, create snapshot for a volume,
and also increase/decrease the volume size online.

DLVM has similar concepts as traditional `LVM`_. The traditional
`LVM`_ has pv (physical volume), vg (volume group) and lv (logical
volume), DLVM has the corresponding conecpts which are dpv, dvg and
dlv, the characture 'd' indicate distribute. In DLVM, a dpv is a linux
server which can export volume(s) through iscsi. A dvg is a group of
dpvs, a dlv is a logical aggregation of dpvs in a same dvg. A dlv can
be attached to a host. On the host, every two dpvs of the dlv will be
aggregated to a device mirror target, and all the device mapper mirror
targets will be aggregated to a device mapper stripe target. The
finally stripe target can be used as a block device on the host. Below
image is a simple example.



.._LVM https://en.wikipedia.org/wiki/Logical_Volume_Manager_%28Linux%29
