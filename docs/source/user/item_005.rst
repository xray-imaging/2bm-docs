Data collection
===============

The tomography scans are managed by the `2bm tomo <https://github.com/xray-imaging/2bm-tomo>`_ python library. Please refer to 
`2bm tomo <https://github.com/xray-imaging/2bm-tomo>`_ for details.

To run a tomographic scan::

    [user2bmb@arcturus,42,~]$ tomo scan

from the command line. To get correct results, you may need to set specific
options, for example to collect 10 tomographic dataset at 10 vertical positions separated by 1 mm::

    [user2bmb@arcturus,42,~]$ tomo scan --scan-type vertical --vertical-scan-start 0 --vertical-scan-end 10 --vertical-scan-step-size 1

to list of all available options::

    [user2bmb@arcturus,42,~]$ tomo scan -h

For more details refer to `2bm tomo <https://github.com/xray-imaging/2bm-tomo>`_.

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
