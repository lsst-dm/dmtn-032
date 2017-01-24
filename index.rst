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
