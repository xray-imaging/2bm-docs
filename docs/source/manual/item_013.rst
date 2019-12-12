Scope Setting
=============

.. contents:: 
   :local:

To operate 2-BM Digita Oscilloscope under epics/medm you neet to start a soft epics IOC with::

    user2bmb@arcturus$ cd /net/s6dserv/xorApps/epics/synApps_5_8/ioc/dlm/iocBoot/iocLinux
    user2bmb@arcturus$ run

then in a different xterm start the medm screen with::

    user2bmb@arcturus$ cd /net/s6dserv/xorApps/epics/synApps_5_8/ioc/dlm
    user2bmb@arcturus$ start_caQtDM

and select

- DLM400 button to start the control screen for the scope then
- caputRec
    - click on (re)start recorder
    - click on Refresh Menu
    - click Select Macro to pick dlm or dummy
    - click DO to collect a plot
- scanH (for Harware-assisted scans)
    - if caputRec is set with dlm macro it collect multiple plots
    - if caputRec is set with dummy macro it .... 


To view the mda files collected by the scan record use::

    /home/beams/MOONEY/bin/mdaExplorer/APSshare/bin/dview

To convert MDA files to ASCII::

    /APSshare/bin/mda2ascii