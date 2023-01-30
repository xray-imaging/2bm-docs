Computing 
=========

The following computers are in use at 2-BM:



.. _cluster_folder: https://anl.box.com/s/cwqbvet2qv8239nhrof0qemyohd0jho3
.. _cluster: https://anl.box.com/s/uysvb5ujnlugmd16r2f6o10fem9rjgvr
.. _disk_array: https://anl.box.com/s/zzyvv7w80ltwbtf09zrjiqiw7ak6i7ge
.. _cluster_quote: https://anl.box.com/s/j7wz6li4afoq2gs5g8feehmmz8q7whuy
.. _disk_array_quote: https://anl.box.com/s/sbft8cbt2xcpzuuvikixr82dn9jf6zog


Data Analysis and Storage
-------------------------

+-----------------------------------------+--------------+---------------+-----------------+---------------------------------+----------------------+
| Task                                    | Name         | Product       | Part list       |      Model                      |      Quote           |
+-----------------------------------------+--------------+---------------+-----------------+---------------------------------+----------------------+
| Tomo Recon                              | tomo 1-2     | MNJ15421064   | `cluster`_      |  Supermicro 740GP-TNRT cluster  | `cluster_quote`_     |
+-----------------------------------------+--------------+---------------+-----------------+---------------------------------+----------------------+
| Data Storage                            | disk array   | MNJ15508749   | `disk_array`_   |  SYS-220U-TNR Storage           | `disk_array_quote`_  |
+-----------------------------------------+--------------+---------------+-----------------+---------------------------------+----------------------+


Data Collection
---------------

+-----------+--------------+-------------------+-----------------+--------------------------+---------------------+
| Station   | Name         |      Model        |  Product No.    |    Serial No.            |        Manual       |
+-----------+--------------+-------------------+-----------------+--------------------------+---------------------+
| 2-BM-A    | pg10ge       |  HP Z8 G4         | 3GF37UT#ABA     |  `pg10ge label`_         |     `pg10ge SM`_    |
+-----------+--------------+-------------------+-----------------+--------------------------+---------------------+
| 2-BM-B    | lyra         |  HP EliteDesk 800 | P4K18UT#ABA     |  `lyra label`_           |     `lyra SM`_      |
+-----------+--------------+-------------------+-----------------+--------------------------+---------------------+

For each machine part list at purchase time and for the list of supported hardware enter the serial numeber in the `HP support <https://partsurfer.hp.com/Search.aspx>`_ web page.

.. _pg10ge label: https://anl.box.com/s/oslaky958be3vyifda2xyq4tv0v9v7pz
.. _pg10ge SM: https://anl.box.com/s/m1u8o62wbr27n26iotfnbhgpncwsapcq
.. _lyra label: https://anl.box.com/s/lrjiwsfzwbe51gueb6vpyinqav86qx6o
.. _lyra SM: https://anl.box.com/s/dv0ub0gdjhs7q3h50ehgro6gaesbxcjf


Data Visualization
------------------

The default computer is **handyn** where the following software is installed:

Raw Data
~~~~~~~~

To view the tomographic raw data we suggest to install `Fiji <https://imagej.net/Fiji>`_ and add 
the `HDF plugin <https://github.com/paulscherrerinstitute/ch.psi.imagej.hdf5>`_

Other options are `hdfview <https://support.hdfgroup.org/products/java/hdfview/>`_ or 
`argos <https://github.com/titusjan/argos>`_

To view the tomographic raw data we suggest to install `Fiji <https://imagej.net/Fiji>`_ and add 
the `HDF plugin <https://github.com/paulscherrerinstitute/ch.psi.imagej.hdf5>`_

Other options are `hdfview <https://support.hdfgroup.org/products/java/hdfview/>`_ or 
`argos <https://github.com/titusjan/argos>`_


Dragonfly
~~~~~~~~~


After your data are reconstructed you can visualize using `Dragonfly <https://www.theobjects.com/dragonfly/index.html>`_.

Login at the beamline Linux machine and then type::

    [tomo@handyn]$ cd /local/tomo/software/dragonfly
    [tomo@handyn]$ ./Dragonfly

