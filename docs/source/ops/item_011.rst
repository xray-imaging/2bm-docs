Logs
====

EPICS PV logging
----------------

To log and beamline PV variable use one of the tools available a `2bm tools <https://github.com/xray-imaging/2bm-tools>`_.

Experiment logging
------------------

To generate user logs to be published on google slides we developed `tomolog <https://tomologcli.readthedocs.io/en/latest/>`_. tomolog creates a google slide by extracting information from the raw hdf tomography data, projection and reconstruction image data.

To view the table for data stored by tomo@handyn::

    tomo@handyn$ bash
    tomo@handyn$ conda activate google
    tomo@handyn$ tomolog run --file-name /data/2022-02/Salcedo/ --rec-type rec

To access/view experiment meta data we developed `meta <https://github.com/xray-imaging/meta>`_ 

For run meta::

    (base) tomo@handyn ~ $ conda activate meta
    (meta) tomo@handyn ~ $ meta show --file-name /local/data/base_file_name_001.h5 

optional arguments:
  -h, --help     show this help message and exit
  --config FILE  File name of configuration

Commands::

    init         Create configuration file
    status       Show meta status
    show         Show meta data extracted from -fname-name
    docs         Create in --doc-dir an rst file compatible with
 

