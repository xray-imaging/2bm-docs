================
Beamline control
================

All beamline components and detectors are controlled using `EPICS <https://epics-controls.org/>`_ and `areaDetector <https://areadetector.github.io/master/index.html>`_.
Each device can be configure and controlled through a graphic user interface (GUI) or through a python script using `PyEpics <https://cars9.uchicago.edu/software/python/pyepics3/>`_.


To start the main 2-BM beamline control user interface::

    [2bmb@arcturus,42,~]$ start_epics

or::

    [2bmb@arcturus,42,~]$ start_tomo


.. image:: ../img/2bma_beamline.png 
   :width: 720px
   :align: center
   :alt: 2bma_beamline

.. image:: ../img/2bmb_beamline.png 
   :width: 720px
   :align: center
   :alt: 2bmb_beamline

.. image:: ../img/tomo_02.png 
   :width: 720px
   :align: center
   :alt: tomo_01


.. warning:: If some of the above screen contains white fields, it means that the corresponding EPICS IOC is not running. To check/start/stop any IOC associated with tomograhy use the ioc_start user interface:

.. image:: ../img/tomo_07.png 
   :width: 340px
   :align: center
   :alt: tomo_07   


==================
Tomography Control
==================

To start the main control screens for 2-BM-B for scanning, streaming data collection or real-time reconstruction select, in the main 2-BM-B beamline control screens, the corresponding screen for scan, stream or recon:


.. image:: ../img/tomo_00.png 
   :width: 256px
   :align: center
   :alt: tomo_01



TomoScan
========

.. figure:: ../img/tomo_03.png 
   :width: 512px
   :align: center
   :alt: tomo_031
   


TomoScanStream
==============

.. figure:: ../img/tomo_05.png 
   :width: 340px
   :align: center
   :alt: tomo_05


TomoStream
==========

.. figure:: ../img/tomo_06.png 
   :width: 340px
   :align: center
   :alt: tomo_06   


