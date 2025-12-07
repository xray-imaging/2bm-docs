====
Logs
====

Tomolog
=======

`tomolog <https://tomologcli.readthedocs.io/en/latest/index.html>`_ is
used to publish tomography experiment data and metadata to a stack of
Google Slides.

Run tomolog::

  $ ssh 2bmb@tomo1
  $ bash
  (base) 2bmb@tomo1 $ conda activate tomolog

.. warning::

   When running tomolog from a private-network computer, follow the
   additional instructions at:
   https://img.xray.aps.anl.gov/source/Networks.html#tomolog

Then execute, for example::

  (tomolog) 2bmb@tomo1 $ tomolog run --file-name /data3/2BM/2025-08/Mittone/Brain_ethanol_mosaic_010.h5


EPICS PV logging
================

To log beamline PV variables, use one of the tools provided in:

- `2bm-tools <https://github.com/xray-imaging/2bm-tools>`_


Experiment logging
==================

Experiment metadata are stored automatically for each scan in the raw data
HDF file. To view and work with these metadata, use
`meta <https://github.com/xray-imaging/meta>`_.

Run meta::

  2bm@tomdet$ bash
  (base) 2bm@tomdet ~ $ conda activate ops
  (ops)  2bm@tomdet ~ $ meta show --file-name /local/data/base_file_name_001.h5

  optional arguments:
    -h, --help     show this help message and exit
    --config FILE  File name of configuration

Commands::

  init         Create configuration file
  status       Show meta status
  show         Show metadata extracted from --file-name
  docs         Create in --doc-dir an .rst file (Sphinx/ReadTheDocs-compatible)
               containing the DataExchange HDF file metadata