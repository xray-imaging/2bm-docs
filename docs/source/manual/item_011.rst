Energy 
======

`energy <https://github.com/decarlof/tomo2bm/blob/master/flir/energy>`_ is a python script that changes the 2-BM beamline energy configuration. 

Usage
-----

::

    user2bmb@acturus% cd ~/MCT/flir/
    user2bmb@acturus% energy mono 24.9
    user2bmb@acturus% energy pink 2.657
    user2bmb@acturus% energy white

for help::

    user2bmb@acturus% energy -h

Testing mode
------------


Set:: 

    TESTING = True 

in `energy_lib <https://github.com/decarlof/tomo2bm/blob/master/flir/libs/energy_lib.py>`_