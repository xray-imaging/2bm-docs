========
Blueesky
========

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


Install directions
==================

Build EPICS base
----------------

.. warning:: Make sure the disk partition hosting ~/epics is not larger than 2 TB. See `tech talk <https://epics.anl.gov/tech-talk/2017/msg00046.php>`_ and  `Diamond Data Storage <https://epics.anl.gov/meetings/2012-10/program/1023-A3_Diamond_Data_Storage.pdf>`_ document.

::

    $ mkdir ~/epics-ad
    $ cd epics-ad
    

- Download EPICS base latest release from https://github.com/epics-base/epics-base::

    $ git clone https://github.com/epics-base/epics-base.git
    $ cd epics-base
    $ git submodule init
    $ git submodule update
    $ make distclean (do this in case there was an OS update)
    $ make -sj

.. warning:: if you get a *configure/os/CONFIG.rhel9-x86_64.Common: No such file or directory* error issue this in your csh termimal: $ **setenv EPICS_HOST_ARCH linux-x86_64**


Build ADSimDetector
-------------------

- Download in ~/epics-ad `assemble_synApps <https://github.com/EPICS-synApps/assemble_synApps/blob/18fff37055bb78bc40a87d3818777adda83c69f9/assemble_synApps>`_.sh
- Edit the assemble_synApps.sh script to include only::
  
    $modules{'ASYN'} = 'R4-44-2';
    $modules{'AUTOSAVE'} = 'R5-11';
    $modules{'AREA_DETECTOR'} = 'R3-12-1';
    $modules{'AREA_DETECTOR_SUBMODULES'} = 'ADSimDetector'; # Space-separated #. list of extra submodules to check out
    $modules{'BUSY'} = 'R1-7-4';
    $modules{'CALC'} = 'R3-7-5';
    $modules{'DEVIOCSTATS'} = '3.1.16';
    $modules{'SSCAN'} = 'R2-11-6';
    $modules{'SNCSEQ'} = 'R2-2-9';
    $modules{'XXX'} = 'R6-3';

You can comment out all of the other modules (ALLENBRADLEY, ALIVE, etc.)

- Run::

    $ cd ~/epics-ad
    $ ./assemble_synApps.sh --dir=synApps --base=/home/beams/FAST/epics-ad/epics-base

.. warning:: replace  */home/beams/FAST/* with the path of your home directory

- This will create a synApps/support directory::

    $ cd synApps/support/

Build with::

    $ make release
    $ make -sj


Testing ADSimDetector
---------------------

::

    cd ~/epics-ad/synApps/support/areaDetector-R3-12-1/ADSimDetector/iocs/simDetectorIOC/iocBoot/iocSimDetector

rename envPaths as envPaths.linux::

    mv envPaths envPaths.linux

edit st.cmd.linux from::

    < envPaths.linux
    < st_base.cmd

to::

    < ./envPaths.linux
    < ./st_base.cmd

edit start_epics from::

    #medm -x -macro "P=13SIM1:, R=cam1:" simDetector.adl &
    ../../bin/linux-x86_64/simDetectorApp st.cmd.linux

to::

    #!/bin/csh
    setenv EPICS_APP_AD /home/beams/FAST/epics-ad/synApps/support/areaDetector-R3-12-1/ADCore
    setenv EPICS_APP_ADSIM /home/beams/FAST/epics-ad/synApps/support/areaDetector-R3-12-1/ADSimDetector
    #####################
    # prepare MEDM path
    #
    if (! ${?EPICS_DISPLAY_PATH}) setenv EPICS_DISPLAY_PATH '.'
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_ADSIM/simDetectorApp/op/adl
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_AD/ADApp/op/adl
    medm -x -macro "P=13SIM1:, R=cam1:" ../../../../simDetectorApp/op/adl/simDetector.adl &
    ../../bin/linux-x86_64/simDetectorApp st.cmd.linux

.. warning:: replace  */home/beams/FAST/* with the path of your home directory

Start ADSimDetector
~~~~~~~~~~~~~~~~~~~

::

    ./start_epics


.. image:: ../img/ADSim_00.png 
   :width: 512px
   :align: center
   :alt: ADSim_00

.. image:: ../img/ADSim_01.png 
   :width: 512px
   :align: center
   :alt: ADSim_01


Install ADAravis
================

Detailed instructions are `here <https://areadetector.github.io/areaDetector/ADAravis/ADAravis.html>`_.

Make sure the `assemble_synApps <https://github.com/EPICS-synApps/assemble_synApps/blob/18fff37055bb78bc40a87d3818777adda83c69f9/assemble_synApps>`_.sh script includes:

::

    $modules{'AREA_DETECTOR_SUBMODULES'} = 'ADAravis ADGenICam'; # Space-separated list of extra 

then run `assemble_synApps <https://github.com/EPICS-synApps/assemble_synApps/blob/18fff37055bb78bc40a87d3818777adda83c69f9/assemble_synApps>`_.sh 

::

    $ cd ~/epics-ad
    $ ./assemble_synApps.sh --dir=synApps --base=/home/beams/FAST/epics-ad/epics-base

.. warning:: replace  */home/beams/FAST/* with the path of your home directory

.. warning:: if you get a  *make: No rule to make target ...  Stop* error issue this in your csh termimal: $ **setenv EPICS_HOST_ARCH linux-x86_64**

and build ADGenICam

::

    $ cd ~/epics-ad/synApps/support/areaDetector-R3-12-1/ADGenICam
    $ make -sj

todo: add instruction on envPaths etc.

Testing ADAravis
----------------

::

    cd areaDetector-R3-11/ADAravis/iocs/aravisIOC/iocBoot/iocAravis

and edit this line:

::

    # Name of camera as reported by arv-tool
    epicsEnvSet("CAMERA_NAME", "FLIR-Oryx ORX-10G-51S5M-18011754")

of the st.cmd.Oryx_51S5 file to add the camera information obtained by the output of the arv-tool:

::

    # Name of camera as reported by arv-tool
    epicsEnvSet("CAMERA_NAME", "FLIR-Oryx ORX-10G-51S5M-19173710")
    epicsEnvSet("CAMERA_ID", "FLIR-Oryx ORX-10G-51S5M-19173710")
    epicsEnvSet("CAMERA_INFO", "FLIR-Oryx ORX-10G-51S5M-19173710 (169.254.0.51)")

edit the start_epics file as follows:

::

    #!/bin/csh
    setenv EPICS_APP_AD /home/beams/FAST/epics-ad/synApps//support/areaDetector-R3-12-1/ADCore
    setenv EPICS_APP_ADGENICAM /home/beams/FAST/epics-ad/synApps//support/areaDetector-R3-12-1/ADGenICam
    setenv EPICS_APP_ADARAVIS /home/beams/FAST/epics-ad/synApps//support/areaDetector-R3-12-1/ADAravis
    #####################
    # prepare MEDM path
    #
    if (! ${?EPICS_DISPLAY_PATH}) setenv EPICS_DISPLAY_PATH '.'
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_ADARAVIS/aravisApp/op/adl
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_ADGENICAM/GenICamApp/op/adl
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_AD/ADApp/op/adl

    medm -x -macro "P=13ARV1:, R=cam1:, C=FLIR-Oryx-ORX-10G-310S9M" ../../../../aravisApp/op/adl/ADAravis.adl &

    ../../bin/linux-x86_64/ADAravisApp st.cmd.Oryx_51S5

.. warning:: replace  */home/beams/FAST/* with the path of your home directory

Start ADAravis
~~~~~~~~~~~~~~

::

    ./start_epics


.. image:: ../img/ADAravis_00.png 
   :width: 512px
   :align: center
   :alt: ADSim_00


Install ADSpinnaker
===================

Detailed instructions are at the `areadetector doc page <https://areadetector.github.io/areaDetector/ADSpinnaker/ADSpinnaker.html>`_.

Make sure the `assemble_synApps <https://github.com/EPICS-synApps/assemble_synApps/blob/18fff37055bb78bc40a87d3818777adda83c69f9/assemble_synApps>`_.sh script includes:

::

    $modules{'AREA_DETECTOR_SUBMODULES'} = 'ADSpinnaker ADGenICam'; # Space-separated list of 


then run `assemble_synApps <https://github.com/EPICS-synApps/assemble_synApps/blob/18fff37055bb78bc40a87d3818777adda83c69f9/assemble_synApps>`_.sh

::

    $ cd ~/epics-ad
    $ ./assemble_synApps.sh --dir=synApps --base=/home/beams/FAST/epics-ad/epics-base

.. warning:: replace  */home/beams/FAST/* with the path of your home directory

.. warning:: if you get a  *make: No rule to make target ...  Stop* error issue this in your csh termimal: $ **setenv EPICS_HOST_ARCH linux-x86_64**

and build ADGenICam

::

    $ cd ~/epics-ad/synApps/support/areaDetector-R3-12-1/ADGenICam
    $ make -sj

then install the `Spinnaker SDK <https://www.flir.com/products/spinnaker-sdk/>`_ must be downloaded and installed on the Windows or Linux machine prior to running the IOC because it installs the necessary drivers. 

todo: add instruction on envPaths etc.

Testing ADSpinnaker
-------------------

::

    cd areaDetector-R3-11/ADSpinnaker/iocs/spinnakerIOC/iocBoot/iocSpinnaker

and edit this line:

::

    # Name of camera as reported by arv-tool
    epicsEnvSet("CAMERA_NAME", "FLIR-Oryx ORX-10G-51S5M-18011754")

of the st.cmd.Oryx_51S5 file to add the camera information obtained by the output of the arv-tool:

::

    # Use this line for a specific camera by serial number, in this case a BlackFlyS GigE
    epicsEnvSet("CAMERA_ID", "19173710")  # 2-BM-B 2bmbSP1:
    epicsEnvSet("CAMERA_INFO", "FLIR-Oryx ORX-10G-51S5M-19173710 (169.254.0.51)")

edit the start_epics file as follows:

::

    #!/bin/csh
    setenv EPICS_APP_AD /home/beams/FAST/epics-ad/synApps//support/areaDetector-R3-12-1/ADCore
    setenv EPICS_APP_ADGENICAM /home/beams/FAST/epics-ad/synApps//support/areaDetector-R3-12-1/ADGenICam
    setenv EPICS_APP_ADSpinnaker /home/beams/FAST/epics-ad/synApps//support/areaDetector-R3-12-1/ADSpinnaker
    #####################
    # prepare MEDM path
    #
    if (! ${?EPICS_DISPLAY_PATH}) setenv EPICS_DISPLAY_PATH '.'
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_ADSpinnaker/spinnakerApp/op/adl
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_ADGENICAM/GenICamApp/op/adl
    setenv EPICS_DISPLAY_PATH $EPICS_DISPLAY_PATH':'$EPICS_APP_AD/ADApp/op/adl

    medm -x -macro "P=13SP1:, R=cam1:, C=FLIR-Oryx-ORX-10G-310S9M" ../../../../spinnakerApp/op/adl/ADSpinnaker.adl &

    ../../bin/linux-x86_64/spinnakerApp st.cmd.oryx_51S5

Start ADSpinnaker
~~~~~~~~~~~~~~~~~

::

    ./start_epics


.. image:: ../img/ADSpinnaker_00.png 
   :width: 512px
   :align: center
   :alt: ADSim_00

arv-tool
========

The arv-tool is part of the Aravis library, typically used for managing and controlling GenICam-compliant cameras. You can extract the camera's XML description file, which contains details about the camera's features and configuration, using the following steps:

::

    git clone https://github.com/AravisProject/aravis.git
    cd aravis

to configure and build it install meson or ninja with::

    pip install --user meson ninja

Adding  ~/.local/bin to your PATH to access them by adding in your .bashrc::

    export PATH=$HOME/.local/bin:$PATH

then build aravis with::

    meson setup builddir --prefix=$HOME/aravis-install
    cd builddir
    meson compile
    meson install

Add the local installation directory to your environment variables::

    export PATH=$HOME/aravis-install/bin:$PATH
    export LD_LIBRARY_PATH=$HOME/aravis-install/lib:$LD_LIBRARY_PATH
    export PKG_CONFIG_PATH=$HOME/aravis-install/lib/pkgconfig:$PKG_CONFIG_PATH
    export GI_TYPELIB_PATH=$HOME/aravis-install/lib/girepository-1.0:$GI_TYPELIB_PATH

then find information about any camera connected to the comuter with::

    arv-tool-0.10 --list


The arv-tool is used to download from the camera the XML file tha will be used to automatically create the camera EPICS data base the MEDM scrrens. Please look at the areadetector documentation `here 2 <https://areadetector.github.io/areaDetector/ADGenICam/ADGenICam.html#downloading-the-xml-file>`_ for more details.


Configure NIC on 10gbit FLIR cameras
====================================


1. Prerequisites:

    64GB memory
    Cat 6A cable
    Intel X550T2 ETHERNET CONVERGED Network Adapter X550-T2

2. Enable jumbo packet
3. Disable DHCP and set a fixed IP address on the Ethernet port connecting to the FLIR
4. Increase the receive buffer size (MTU ~ 9000)
5. Increase the Network parameters in the kernel
6. Set the NIC tx queue length

1. is available from Sorcium as Part#: 3E9073

2. 3. and 4. are documented at:

     FLIR doc: https://www.flir.com/support-center/iis/machine-vision/knowledge-base/lost-ethernet-data-packets-on-linux-systems/

4. is documented both at flir doc and in the areadetector doc:

    FLIR doc: https://www.flir.com/support-center/iis/machine-vision/knowledge-base/lost-ethernet-data-packets-on-linux-systems/

    areadetector doc: https://areadetector.github.io/master/ADGenICam/ADGenICam.html#linux-usb-and-gige-system-settings

5. edit /etc/sysctl.conf and add:

    net.core.rmem_default=26214400
    net.core.rmem_max=268435456 

6. edit /etc/rc.local and add:

    #NIC camera settings and  10GB nic settings  In this example the camera is attached to  ens1f1    
    /usr/sbin/ifconfig ens1f1 txqueuelen 3000 (this is hardware specific . i.e. this card  supports up to 4096, some max out at about 512 etc.)
