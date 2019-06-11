Data reconstruction
===================

At the APS
----------

Your raw data are automatically copied from the detector to the analysis computer (handyn in this example) under the folder /local/data/YYYY-MM/PI_lastName. After the transfer the data are also automatically reconstructed with:: 

	rec --type try --srs 30  /local/data/YYYY-MM/PI_lastName/file.h5 


Login at the beamline Linux machine handyn as user “tomo” then type::

    [tomo@handyn,~]$ rec -h


for help. More detailed instruction are at https://github.com/decarlof/util/tree/master/recon

To do a test reconstruction just type::

    tomo@handyn,~]$ rec.py /local/data/YYYY-MM/PI_lastName/file.h5 


At your home institution
------------------------

Install the following::

    Conda: https://www.anaconda.com/download/
    Tomopy: conda install -c conda-forge tomopy

then copy from https://github.com/decarlof/util/tree/master/recon in your python working directory::

    find_center.py
    rec.py
