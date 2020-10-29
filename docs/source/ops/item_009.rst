Logs
====

To generate user logs table to be published `here <https://docs2bm.readthedocs.io/en/latest/source/logs.html>`_ use `meta5 <https://github.com/xray-imaging/metah5>`_ to generate a table in rst format. 

metah5 creates this table automatically by extracting information from the raw hdf tomography data.


To view the table for data stored by tomo@handyn::

    tomo@handyn$ bash
    tomo@handyn$ metah5 show --h5-name /local/data/2020-10/Peng/

To publish the table for data stored by tomo@handyn::

    tomo@handyn$ bash
    tomo@handyn$ metah5 docs --h5-name /local/data/2020-10/Peng/
    2020-10-28 19:31:41,004 - General
    2020-10-28 19:31:41,004 -   config           /home/beams/TOMO/metah5.conf
    2020-10-28 19:31:41,004 -   verbose          True
    /local/tomo/conda/2bm-docs/docs/source/logs/log_2020-10.rst
    tomo@handyn$ cd /local/tomo/conda/2bm-docs/
    tomo@handyn$ git add -u
    tomo@handyn$ git commit -m "adding 2020-10/Peng to the log"
    tomo@handyn$ git push origin master

The new table will be published at  `beamline doc <https://docs2bm.readthedocs.io/en/latest/source/logs.html>`_ 

For help::

    (base) tomo@handyn ~ $ metah5 -h
    usage: metah5 [-h] [--config FILE]  ...

optional arguments:
  -h, --help     show this help message and exit
  --config FILE  File name of configuration

Commands:
  
    init         Create configuration file
    show         Show meta data extracted from --h5-name
    docs         Create in --doc-dir an rst file compatible with
 

