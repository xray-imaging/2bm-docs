================
Beamline control
================

All beamline components and detectors are controlled using
`EPICS <https://epics-controls.org/>`_ and
`areaDetector <https://areadetector.github.io/master/index.html>`_.

Each device can be configured and controlled either through a graphical
user interface (GUI) or from Python using
`PyEpics <https://cars9.uchicago.edu/software/python/pyepics3/>`_.

To start the main 2-BM beamline control user interface, run::

  [2bmb@arcturus,42,~]$ start_epics

To open the tomography administrative screens directly, use::

  [2bmb@arcturus,42,~]$ start_tomo_scan
  [2bmb@arcturus,42,~]$ start_tomo_stream

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
   :alt: tomo_02

.. warning::

   If any of the screens above show white fields, the corresponding EPICS
   IOC is not running. To check, start, or stop any IOC associated with
   tomography, use the ``ioc_start`` user interface:

   .. image:: ../img/tomo_07.png
      :width: 340px
      :align: center
      :alt: tomo_07