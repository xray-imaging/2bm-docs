========
Softglue
========

Start the epics IOC with:

::

   [2bmb@arcturus ~]$ /net/s2dserv/xorApps/epics/synApps_SG/ioc/2bmbMZ1/iocBoot/ioc2bmbMZ1
   [2bmb@arcturus ~]$ ../../bin/rhel9-x86_64/GenLuaModules.pl st.cmd  


::

   [2bmb@arcturus ~]$ cd /net/s2dserv/xorApps/epics/synApps_SG/ioc/2bmbMZ1/
   [2bmb@arcturus ~]$ ./start_epics_2bmbMZ1
or

::

   [2bmb@arcturus 2bmbMZ1]$ cd /net/s2dserv/xorApps/epics/synApps_SG/ioc/2bmbMZ1/
   [2bmb@arcturus 2bmbMZ1]$ ./start_caQtDM_2bmbMZ1 &

to get:

.. figure:: ../img/softglue_001.png
   :width: 360px
   :align: center
   :alt: softglue_001

   Softglue control

then select softGlue/softGlueZynqMenu to get:

.. figure:: ../img/softglue_002.png
   :width: 128px
   :align: center
   :alt: softglue_002

   Softglue control

then select Collections/all softGlueZynq

.. figure:: ../img/softglue_003.png
   :width: 360px
   :align: center
   :alt: softglue_003

   Softglue control

The location of the pulses is configurable by python using the `write_pso_array <https://github.com/decarlof/interlaced/blob/main/macros_ILF.py>`_ function.

::


   (ops) 2bmb@arcturus]$ python
   Python 3.12.2 | packaged by conda-forge | (main, Feb 16 2024, 20:50:58) [GCC 12.3.0] on linux
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import macros_ILF as m
   >>> m.write_PSO_array([0,2,4,6])
   >>> 


the list of positions [0,2,4,6] is in encoder pulses.