=======
Bluesky
=======

The beamline utilizes `Bluesky <https://nsls-ii.github.io/bluesky/>`_ in conjunction with `tomo bits <https://github.com/BCDA-APS/tomo-bits/tree/main>`_ for data acquisition and device control.

To begin, activate the appropriate conda environment::

    (base) 2bmb@arcturus ~ $ conda activate tomo-bits-decarlof

Then start an IPython session::

    (tomo-bits-decarlof) 2bmb@arcturus ~ $ ipython
    Python 3.11.12 | packaged by conda-forge | (main, Apr 10 2025, 22:23:25) [GCC 13.3.0]
    Type 'copyright', 'credits' or 'license' for more information
    IPython 9.2.0 -- An enhanced Interactive Python. Type '?' for help.
    Tip: The `%timeit` magic has a `-o` flag, which returns the results, making it easy to plot. See `%timeit?`.

Loading Beamline Devices
========================

To initialize the beamline environment and load all devices, run::

    In [1]: from tomo_instrument.startup import *
    I Mon-15:25:45.117: **************************************** Bluesky Startup

You'll see output indicating the system is starting up::

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

The configuration process loads and registers all relevant devices::

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

This indicates that the following devices are now available in the IPython namespace:

    #. shutter
    #. optics
    #. sim_motor
    #. sim_det
    #. aps (from APS-specific config)

optics provides Bluesky support for `mctOptics <https://mctoptics.readthedocs.io/en/latest/>`_, which interfaces with the Optic Peter system—a dual-camera, triple-lens optical assembly used at the beamline. To change lens::

    In [4]: optics.lens_select.get()
    Out[4]: 2
    In [10]: optics.lens_select.put(1)
    In [11]: optics.lens_select.get()
    Out[11]: 1

to change camera::

    In [14]: optics.camera_select.get()
    Out[14]: 0
    In [15]: optics.camera_select.put(1)
    In [16]: optics.camera_select.get()
    Out[16]: 1

