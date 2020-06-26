Data collection
===============

The tomography scans are managed by `tomoScan <https://tomoscan.readthedocs.io/en/latest/index.html>`_. Please refer to `tomoScan <https://tomoscan.readthedocs.io/en/latest/index.html>`_ for details.

To configure a single tomographic scan enter the acquistion parameters at:

.. image:: ../img/tomoScan.png
   :width: 480px
   :align: center
   :alt: tomoScan


To run a single scan with the parameters set in the tomoScan screen press the gree **Start Scan** button. To collect the same from the command line interface::

    [user2bmb@arcturus,42,~]$ tomoscan single

tomoscan supports also vertical, horizontal and mosaic tomographic scans with::

    [user2bmb@pg10ge]$ tomoscan vertical
    [user2bmb@pg10ge]$ tomoscan horizontal
    [user2bmb@pg10ge]$ tomoscan mosaic

to run a vertical scan::

    $ [user2bmb@pg10ge]$ tomoscan vertical --vertical-start 0 --vertical-step-size 0.1 --vertical-steps 2

    2020-05-29 16:54:03,354 - vertical scan start
    2020-05-29 16:54:03,356 - vertical positions (mm): [0.  0.1]
    2020-05-29 16:54:03,358 - SampleInY stage start position: 0.000 mm
    2020-05-29 16:54:03,362 - single scan start
    2020-05-29 16:54:51,653 - single scan time: 0.805 minutes
    2020-05-29 16:54:51,654 - SampleInY stage start position: 0.100 mm
    2020-05-29 16:54:51,658 - single scan start
    2020-05-29 16:55:47,607 - single scan time: 0.932 minutes
    2020-05-29 16:55:47,607 - vertical scan time: 1.738 minutes
    2020-05-29 16:55:47,608 - vertical scan end


please check the `command line manual  <https://tomoscan.readthedocs.io/en/latest/demo.html#using-the-tomoscan-cli>`_ for more details. 

Bluesky
-------

.. contents:: 
   :local:

To operate 2-BM using bluesky (currently in beta test in 2-BM-B) type::

    user2bmb@lyra$ use_bluesky.sh 2bmb

Once in the ipython shell type::

    RE(user_tomo_scan(), comment="my tomo fly scan", sample="wood stick")

or::

    RE(user_tomo_scan(acquire_time=0.1), comment="my tomo fly scan", sample="wood stick")
    RE(user_tomo_scan(acquire_time=0.1, iteration=10), comment="my tomo fly scan", sample="wood stick")


Raw Data Viewer 
===============

To view the tomographic raw data we suggest to install `Fiji <https://imagej.net/Fiji>`_ and add 
the `HDF plugin <https://github.com/paulscherrerinstitute/ch.psi.imagej.hdf5>`_

Other options are `hdfview <https://support.hdfgroup.org/products/java/hdfview/>`_ or 
`argos <https://github.com/titusjan/argos>`_
