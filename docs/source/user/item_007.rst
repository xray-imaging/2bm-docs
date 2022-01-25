Data download
=============

Automatic
---------

At the beginning of the beamtime all users listed in the proposal receive an email with a direct link and instructions on how to download data from the APS.

Data sets are distributed using a `Globus <https://www.globus.org>`_, to use it you need to create 
a `Globus Account <https://docs.globus.org/how-to/get-started/>`_  and set up you computer as 
a `Globus EndPoint <https://www.globus.org/globus-connect-personal>`_.


Manual
------
**Note** This only applies if your data were stored on the APS data magement system.

Please follow these steps:

- login into Globus (with your personal globus credential)
- go to "Collection/Search" and search for the aps data select aps#data
- login in the the APS data management system using the same badge number/password combination that use to access the APS poroposal system but put a "d" in front of the badge number
- if you forgot your password you can reset it `here <https://beam.aps.anl.gov/pls/apsweb/forgot_password.start_process>`_
- go to /gdata/dm/2BM/ then seach for your data by year-month/PI last name
- set an end point on your computer (see `Globus EndPoint <https://www.globus.org/globus-connect-personal>`_) 
- download the data!


InfiniBand connection
---------------------

Machines tomodata1, tomo1, and tomo2 are conencted via Mellanox Technologies MT28908 Family [ConnectX-6] rate: 100 Gb/s (4X EDR). For testing data transfer speed:

on tomo@tomo1 run a server as::

  sudo /bin/iperf3 -s -B 117.17.1.51 

on tomo@tomo2 run a client as::

  sudo /bin/iperf3 -c 117.17.1.51 -B 117.17.1.52



Raw Data Viewer 
---------------

To view the tomographic raw data we suggest to install `Fiji <https://imagej.net/Fiji>`_ and add 
the `HDF plugin <https://github.com/paulscherrerinstitute/ch.psi.imagej.hdf5>`_

Other options are `hdfview <https://support.hdfgroup.org/products/java/hdfview/>`_ or 
`argos <https://github.com/titusjan/argos>`_

To view the tomographic raw data we suggest to install `Fiji <https://imagej.net/Fiji>`_ and add 
the `HDF plugin <https://github.com/paulscherrerinstitute/ch.psi.imagej.hdf5>`_

Other options are `hdfview <https://support.hdfgroup.org/products/java/hdfview/>`_ or 
`argos <https://github.com/titusjan/argos>`_
