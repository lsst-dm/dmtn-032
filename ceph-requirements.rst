Current status
==============

The Galactica platform https://galactica.isima.fr is an OpenStack computing system which relies on a Ceph cluster storage.

It is currently used for a variety of research projects, including Big Data. Qserv S15 Large Scale Test dataset https://confluence.lsstcorp.org/display/DM/S15+Large+Scale+Tests uses almost 1/3 of our storage capacity and 1/10 of our computing capacity.

.. figure:: /_static/galactica-current-architecture.jpeg
    :name: galactica-current-architecture

 Currently, the Ceph storage is fully shared between all users and due to the size of Qserv dataset compared to our storage capacity, it has a huge impact on it. This setup has 2 main drawbacks:
* Sysadmin can not change any parameter on Ceph without impacting all other projects (like create/delete pools for Qserv),
* Qserv has a huge pressure on storage which could leads to slow down all other projects.

As a consequence, Galactica project manager would recommend the following design:

.. figure:: /_static/galactica-architecture-proposal.jpeg
    :name: galactica-architecture-proposal

Galactica project manager propose to share computes servers since the workload is mainly data transfert type and the number of VM is bounded. But the storage itself must be separated on dedicated servers. Only the CEPH OSD (Object Storage Daemon), which manage date hard drives, needs to be on dedicated servers. However, Qserv can use the global Ceph manager. Galactica project manager propose to create one to many dedicated pool on those separates servers, and check which setup is the best.

Benefits
========

Galactica project manager could change anything on Ceph parameters without disturbing other projects,
If Qserv IOPS (I/O per seconds) are too high, only the OSD which host Qserv data would be impacted.

Requirements
============

Hardware
--------

To host S15 dataset, i.e. 35TB of data, we would need 2 additional storage servers.
These servers would fit well with our existing platform :
    - Dell R740xD : 25TB, 14K euros, including 7 years warranty, SLA : Day +1

Manpower
--------

Galactica project manager:
* manage the installation and integration of those servers in our existing platform
* work with Qserv team to benchmark and improve Qserv for OpenStack/Ceph.
