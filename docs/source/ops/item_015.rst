=======
Bluesky
=======

The beamline uses `Bluesky <https://nsls-ii.github.io/bluesky/>`_ together
with `tomo bits <https://github.com/BCDA-APS/tomo-bits/tree/main>`_ for
data acquisition and device control.

Environment setup
=================

Activate the appropriate conda environment::

  (base) 2bmb@arcturus ~ $ conda activate tomo-bits-decarlof

Start an IPython session::

  (tomo-bits-decarlof) 2bmb@arcturus ~ $ ipython
  Python 3.11.12 | packaged by conda-forge | (main, Apr 10 2025, 22:23:25) [GCC 13.3.0]
  Type 'copyright', 'credits' or 'license' for more information
  IPython 9.2.0 -- An enhanced Interactive Python. Type '?' for help.
  Tip: The `%timeit` magic has a `-o` flag, which returns the results, making it easy to plot. See `%timeit?`.

Loading beamline devices
========================

Initialize the beamline environment and load all devices::

  In [1]: from tomo_instrument.startup import *
  I Mon-15:25:45.117: **************************************** Bluesky Startup

IPython logging is configured automatically, for example::

  Below are the IPython logging settings for your session.
  These settings have no impact on your experiment.

  Activating auto-logging. Current session state plus future input saved.
  Filename       : /home/beams0/2BMB/.logs/ipython_log.py
  Mode           : rotate
  Output logging : True
  Raw input log  : False
  Timestamping   : True
  State          : active
  Exception reporting mode: Plain

  End of IPython settings

The startup sequence loads configuration files and registers devices::

  I Mon-15:25:52.441: Starting Instrument with iconfig: /home/beams0/2BMB/conda/tomo-bits-decarlof/src/tomo_instrument/configs/iconfig.yml
  W Mon-15:25:52.462: APS DM setup file does not exist: '/home/dm/etc/dm.setup.sh'
  I Mon-15:25:52.463: Registered Bluesky IPython magics
  I Mon-15:25:53.067: Databroker catalog name: temp
  I Mon-15:25:53.068: using ophyd control layer: 'pyepics'
  I Mon-15:25:53.069: RunEngine metadata saved to: .re_md_dict.yml
  I Mon-15:25:54.248: SPEC data file: /home/beams0/2BMB/20250512-152554.dat
  Warning: Ignoring XDG_SESSION_TYPE=wayland on Gnome. Use QT_QPA_PLATFORM=wayland to run on Wayland anyway.
  I Mon-15:25:54.250: Loading device file: /home/beams0/2BMB/conda/tomo-bits-decarlof/src/tomo_instrument/configs/devices.yml
  I Mon-15:25:54.828: Devices loaded in 0.577 s.
  I Mon-15:25:54.829: Adding ophyd device 'shutter' to main namespace
  I Mon-15:25:54.829: Adding ophyd device 'optics' to main namespace
  I Mon-15:25:54.830: Adding ophyd device 'sim_motor' to main namespace
  I Mon-15:25:54.830: Adding ophyd device 'sim_det' to main namespace
  I Mon-15:25:55.883: Loading device file: /home/beams0/2BMB/conda/tomo-bits-decarlof/src/tomo_instrument/configs/devices_aps_only.yml
  I Mon-15:25:55.894: Devices loaded in 0.010 s.
  I Mon-15:25:55.898: Adding ophyd device 'shutter' to main namespace
  I Mon-15:25:55.899: Adding ophyd device 'sim_motor' to main namespace
  I Mon-15:25:55.900: Adding ophyd device 'optics' to main namespace
  I Mon-15:25:55.901: Adding ophyd device 'aps' to main namespace
  I Mon-15:25:55.901: Adding ophyd device 'sim_det' to main namespace

  In [2]: 

After startup, the following devices are available in the IPython namespace:

#. ``shutter``
#. ``optics``
#. ``sim_motor``
#. ``sim_det``
#. ``aps`` (from the APS-specific configuration)


Optical system control (optics)
===============================

The ``optics`` device provides Bluesky access to
`mctOptics <https://mctoptics.readthedocs.io/en/latest/>`_, which controls
the Optic Peter system (dual-camera, triple-lens optical assembly).

Lens selection
--------------

Read the current lens selection::

  In [4]: optics.lens_select.get()
  Out[4]: 2

Select a different lens::

  In [10]: optics.lens_select.put(1)
  In [11]: optics.lens_select.get()
  Out[11]: 1

Camera selection
----------------

Read the current camera selection::

  In [14]: optics.camera_select.get()
  Out[14]: 0

Select a different camera::

  In [15]: optics.camera_select.put(1)
  In [16]: optics.camera_select.get()
  Out[16]: 1