Logs
====

To generate user logs table to be published `here <https://docs2bm.readthedocs.io/en/latest/source/logs.html>`_ use `meta5 <https://github.com/xray-imaging/metah5>`_ to generate a table in rst format. This table is created extracting information from the raw hdf tomography data.

To view the table::

    user2bmb@pg10ge:/local/data/2020-06/pasha_test$ metah5 show

to create the table::

    user2bmb@pg10ge:/local/data/2020-06/pasha_test$ metah5 docs
    astropy module not found
    2020-07-22 13:00:09,503 - General
    2020-07-22 13:00:09,503 -   config           /home/user2bmb/metah5.conf
    2020-07-22 13:00:09,504 -   verbose          True
    ./log_2020-06.rst

The table is stored in the ./log_2020-06.rst to publish this on the web::

    user2bmb@pg10ge:/local/data/2020-06/pasha_test$ cp log_2020-06.rst /local/user2bmb/conda/2bm-docs/docs/source/logs/
    user2bmb@pg10ge:/local/user2bmb/conda/2bm-docs$ cd /local/user2bmb/conda/2bm-docs/
    user2bmb@pg10ge:/local/user2bmb/conda/2bm-docs$ git add -u  
    user2bmb@pg10ge:/local/user2bmb/conda/2bm-docs$ git commit -m "added 2020-06 samples to the log"
    user2bmb@pg10ge:/local/user2bmb/conda/2bm-docs$ git push origin master

The new log will be published in the  `beamline doc <https://docs2bm.readthedocs.io/en/latest/source/logs.html>`_ 