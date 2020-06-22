Alignment
=========

`Adjust <https://github.com/xray-imaging/adjust>`_ is a python script that automates all tomography instrument alignemt taks.

`Adjust <https://github.com/xray-imaging/adjust>`_  works in combination with a 0.5 mm `tungsten sphere <https://www.vxb.com/0-5mm-Tungsten-Carbide-One-0-0197-inch-Dia-p/0-5mmtungstenballs.htm>`_ that needs to be installed as a sample on top of the rotary stage making sure is in the field of view at least when the rotation axis is at 0 and 10 degrees.

`Adjust <https://github.com/xray-imaging/adjust>`_'s funtions include automatic finding of:

- detector pixel size
- scintillator focus location
- rotation axis location
- centering of the sample on the rotation axis
- rotation axis pitch and roll

First step is to mesaure the image pixel size by running::

    user2bmb@pg10ge $ adjust resolution

then::

    user2bmb@pg10ge $ adjust focus
    user2bmb@pg10ge $ adjust center
    user2bmb@pg10ge $ adjust roll
    user2bmb@pg10ge $ adjust pitch


