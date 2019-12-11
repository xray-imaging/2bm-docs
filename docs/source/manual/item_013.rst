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

and select the DLM400 button
