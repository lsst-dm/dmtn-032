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

.. note::

   **This technote is not yet published.**

Requirements
============

Provide a sharp requirements list for Data-Placement is critical in order to
offer an efficient and well-fitted solution.

Maximize high availability
    SQL user queries must continue in real time in case some worker nodes crashes.

Support Disaster recovery
    Define wich critical functions must be supported

Minimize overall cost
    Costs include infrastructure, sofware development, system administration and
    maintenance.

Minimize data reconstruction time
    A node which crashes must be cloned in a minimal amount of time (data and application)

Prevent data loss
    At least one instance of each chunk must be available at any time on disk.
    Archive may be managed by the system?

Maximize performances
    - Bottleneck must be identified and quantified for each proposed architecture.
    - Chunk placement combinations can be optimized depending on a given query set.

Data throughput
---------------

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

    +------------+----------------------+-----------------------+------------------------+
    | Query type | Average query number | Average query latency | Data read by one query |
    |            | at a given time      | (hour)                | (TB)                   |
    +============+======================+=======================+========================+
    | 1          | 16                   | 1                     | 107                    |
    +------------+----------------------+-----------------------+------------------------+
    | 2          | 12                   | 12                    | 5000+107               |
    +------------+----------------------+-----------------------+------------------------+
    | 3          | 12                   | 12                    | 1900+107               |
    +------------+----------------------+-----------------------+------------------------+
    | 4          | 16                   | 16                    | 107+55                 |
    +------------+----------------------+-----------------------+------------------------+
    
.. table:: The estimated data throughput for one node, assuming Qserv cluster is build with 500 nodes

    +------------+------------------------------+
    | Query type | Data read by one query       |
    |            | in one hour on one node (TB) |
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


