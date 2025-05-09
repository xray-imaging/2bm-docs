====
Logs
====

EPICS PV logging
----------------

To log and beamline PV variable use one of the tools available a `2bm tools <https://github.com/xray-imaging/2bm-tools>`_.

Experiment logging
------------------

Experiment meta data are stored automatically for each scan in the rwa data hdf file. 
To access/view experiment meta data we developed `meta <https://github.com/xray-imaging/meta>`_ 

To run meta::

    2bm@tomdet$ bash
    (base) 2bm@tomdet ~ $ conda activate ops
    (ops) 2bm@tomdet ~ $ meta show --file-name /local/data/base_file_name_001.h5 

optional arguments:
  -h, --help     show this help message and exit
  --config FILE  File name of configuration

Commands::

    init         Create configuration file
    status       Show meta status
    show         Show meta data extracted from -fname-name
    docs         Create in --doc-dir an rst file compatible with sphinx/readthedocs containing the DataExchange hdf file meta data

