Detector restart
================

.. contents:: 
   :local:

Flir
----

The area detector IOC controlling the FLIR camera status can be checked with::

    user2bmb@pg10ge$ ~/2bmbOryx.sh status

To stop/restart area detector use ::

    user2bmb@pg10ge$ ~/2bmbOryx.sh start
    user2bmb@pg10ge$ ~/2bmbOryx.sh stop


To power cycle the camera unplug the powersupply at:

.. image:: ../img/camera.png 
   :width: 720px
   :align: center
   :alt: tomo_user


If the area detector IOC controlling the FLIR camera fails to boot even after a camera power cycle, it means that the last auto save file (auto_setting.sav) is corrupted. To recover you need to replace the corrupted auto save with a good one::


    user2bmb@pg10ge$ ~/2bmbOryx.sh stop
    user2bmb@pg10ge$ cd ~/iocSpinnaker/autosave/
    user2bmb@pg10ge$ cp auto_settings.sav_good auto_settings.sav

then restart areadetector with::

    user2bmb@pg10ge$ ~/2bmbOryx.sh start
