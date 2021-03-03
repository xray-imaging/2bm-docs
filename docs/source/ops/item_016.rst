Reference
=========

Here you can find links to  2-BM technical reference material like drawings, hardware manuals etc.

Source
------

+-----------------------------------------+-------------+------------------------+
|  Parameters                             |    Value    |       Units            |
+-----------------------------------------+-------------+------------------------+
|  Storage Ring Electron Energy           |    7.0      |       GeV              |
+-----------------------------------------+-------------+------------------------+
|  Storage Ring Electron Current          |    100.0    |       mA               |
+-----------------------------------------+-------------+------------------------+
|  Bend Radius                            |    38.96    |       m                |
+-----------------------------------------+-------------+------------------------+
|  Peak Magnetic Field                    |    0.6      |       Tesla            |
+-----------------------------------------+-------------+------------------------+
|  Critical X-ray Energy                  |    19.5     |       keV              |
+-----------------------------------------+-------------+------------------------+
|  Horizontal Source Size                 |    198      |       μm FWHM          |
+-----------------------------------------+-------------+------------------------+
|  Vertical Source Size                   |    78       |       μm FWHM          |
+-----------------------------------------+-------------+------------------------+
|  Vertical Source Divergence at 11 keV   |    157      |       μ rad FWHM       |
+-----------------------------------------+-------------+------------------------+
|  Flux at 19.5 keV                       |  1.12x10^13 |    /sec/0.1%BW/mrad(H) | 
+-----------------------------------------+-------------+------------------------+

Filters
-------

.. image:: ../img/filters.png 
   :width: 240px
   :align: center
   :alt: filters


Mirror
------

+-------------+----------------------+
| Coating     | Nominal angle (mrad) |
+-------------+----------------------+
| Cr          |      2.657           |
+-------------+----------------------+
| Pt          |      2.657           |
+-------------+----------------------+

DMM Multi-layer
---------------

2-BM has a double crystal multi-layer monochromator (DMM) to change energy. 
The beamline x-ray energy change is managed by the `energy cli <https://github.com/xray-imaging/2bm-ops>`_ python library. 

Login into user2bmb@arcturus then::

    [user2bmb@arcturus,42,~]$ bash
    [user2bmb@arcturus,42,~]$ energy set --mode Mono --energy-value 20

for help::

    energy -h

More detailed instructions are here the `energy cli <https://github.com/xray-imaging/2bm-ops>`_

Technical infomation about the DMM are available at the links below:


+-----------+--------------+-------------------+---------------------------------------------------------+
| Station   | Descriptiom  |   Images          |   Info                                                  | 
+-----------+--------------+-------------------+---------------------------------------------------------+
| 2-BM-A    |     DMM      | |00001|, |00002|  | `drawings`_, `crystals specs`_, `documentation folder`_ |
+-----------+--------------+-------------------+---------------------------------------------------------+


.. |00001| image:: ../img/dmm_01.png 
    :width: 20pt
    :height: 20pt

.. |00002| image:: ../img/dmm_02.png 
    :width: 20pt
    :height: 20pt

.. _drawings: https://anl.box.com/s/0sa7gjm3nbmacwjknxth0k98y21sa7iy
.. _crystals specs: https://anl.box.com/s/4o7fewu63rwm2tj0l9ezr79ccjozyn77
.. _documentation folder: https://anl.box.com/s/w1eg4cxw43715bnzk8jcg3hd64rdnsdl

