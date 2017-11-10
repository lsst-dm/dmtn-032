..
  Technote content.

  See https://developer.lsst.io/docs/rst_styleguide.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label
     :target: http://target.link/url

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. Add content below. Do not include the document title.

Galactica setup
+++++++++++++++

Current status
==============

The Galactica platform https://galactica.isima.fr is an OpenStack computing system which relies on a Ceph cluster storage.

It is currently used for a variety of research projects, including Big Data. Qserv S15 Large Scale Test dataset https://confluence.lsstcorp.org/display/DM/S15+Large+Scale+Tests uses almost 1/3 of our storage capacity and 1/10 of our computing capacity.

.. figure:: /_static/galactica-current-architecture.jpg
    :name: galactica-current-architecture

Currently, the Ceph storage is fully shared between all users and due to the size of Qserv dataset compared to our storage capacity, it has a huge impact on it. This setup has 2 main drawbacks:
* Sysadmin can not change any parameter on Ceph without impacting all other projects (like create/delete pools for Qserv),
* Qserv has a huge pressure on storage which could leads to slow down all other projects.

As a consequence, Galactica project manager would recommend the following design:

.. figure:: /_static/galactica-architecture-proposal.jpg
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
* Dell R740xD : 25TB, 14K euros, including 7 years warranty, SLA : Day +1

Manpower
--------

Galactica project manager:
* manage the installation and integration of those servers in our existing platform
* work with Qserv team to benchmark and improve Qserv for OpenStack/Ceph.

Future tracks
+++++++++++++

Requirements
============

Provide a sharp requirements list for Data-Placement is critical in order to
offer an efficient and well-fitted solution.

Maximize high availability
    SQL user queries must continue in real time, if less than 2% worker nodes
    crashes. 

Minimize overall cost
    Costs include infrastructure, sofware development, system administration and
    maintenance.

Minimize data reconstruction time
    A node which crashes must be cloned in a minimal amount of time (data and application)

Prevent data loss
    At least one instance of each chunk must be available at any time on disk,
    even in case 5% of storage is definitly lost.
    Archive should also be managed by the system in order to reduce cost. 

Maximize performances
    - Bottleneck must be identified and quantified for each proposed architecture.
    - Chunk placement combinations can be optimized depending on a given query set.

Support Disaster recovery
    At first glance, disaster recovery looks like a separate, likely unrelated concern.
    However, when considering operations for Qserv instead of just the standing up of Qserv,
    this all become related.
    A first requirement is that the data recovery loading method must take into account changes
    in hardware configs between initial load and recovery.

Data throughput
===============

Based on http://ldm-135.readthedocs.io/en/master/#requirements

In order to provide a lower bound for data throughput, problem is simplified: 

- low volume queries are ignored.
- high volume queries are splitted in following groups:

    1. full scan of Object table
    2. joins of the Source tables with the most frequent accessed columns in the Object table
    3. joins of the ForcedSource tables with the most frequent accessed columns in the Object table
    4. scans of the full Object table or joins of it with up to three additional tables other than Source and ForcedSource

.. _tab-estimated-data-throughput:

.. table:: The estimated data throughput for concurrent high volumes queries

    +------------+-----------------+-------------------+----------------------+
    | Query type | Avg query number| Avg query latency | Data read by 1 query |
    |            | at time t       | (hour)            | (TB)                 |
    +============+=================+===================+======================+
    | 1          | 16              | 1                 | 107                  |
    +------------+-----------------+-------------------+----------------------+
    | 2          | 12              | 12                | 5107                 |
    +------------+-----------------+-------------------+----------------------+
    | 3          | 12              | 12                | 2007                 |
    +------------+-----------------+-------------------+----------------------+
    | 4          | 16              | 16                | 163                  |
    +------------+-----------------+-------------------+----------------------+
    
.. table:: The estimated data throughput for one node, for a 500 nodes cluster

    +------------+------------------------------+
    | Query type | Data read by 1 query         |
    |            | in 1 hour on 1 node (TB)     |
    +============+==============================+
    | 1          | 0.214                        |
    +------------+------------------------------+
    | 2          | 0.8511                       |
    +------------+------------------------------+
    | 3          | 0.3345                       |
    +------------+------------------------------+
    | 4          | 0.02025                      |
    +------------+------------------------------+


.. Formula: 0.214+0.8511+0.3345+0.02025 = 1.41985

**With optimal shared scans** (i.e. each table is only read once for concurrent queries), data throughput on one node is: **394 MB/sec**

.. Formula: 0.214*16+0.8511*12+0.3345*12+0.02025*16 = 17.9752

**Without shared scans**, data throughput on one node is: **4993 MB/sec**


