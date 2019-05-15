Data reconstruction
===================

At the APS
----------

Login at the beamline Linux machine handyn as user “tomo” then type::

    [tomo@handyn,~]$ cd /local/tomo/conda/util/recon/

your raw data should be copied from the detector computer to the /local/data folder of handyn::

    ssh –Y tomo@handyn

if the detector runs on lyra this is done with::

    [tomo@handyn,~]$ scp -r user2bmb@lyra:/local/data/raw_data_location/file.h5  /local/data/

then run rec.py as::

    [tomo@handyn,~]$ bash
    [tomo@handyn,~]$ python rec.py -h


for help and use the instruction at https://github.com/decarlof/util/tree/master/recon for selecting different reconstruction methods/options. To do a test reconstruction just type::

    tomo@handyn,~]$ python rec.py /local/data/raw_data_location/file.h5 

At your home institution
------------------------

Install the following::

    Conda: https://www.anaconda.com/download/
    Tomopy: conda install -c conda-forge tomopy

then copy from https://github.com/decarlof/util/tree/master/recon in your python working directory::

    find_center.py
    rec.py
