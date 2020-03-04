Detector restart
================

.. contents:: 
   :local:

Flir
----

The area detector IOC controlling the FLIR camera run on the linux machine named pg10ge.
To check the area detector status::

    user2bmb@pg10ge$ ~/2bmbOryx.sh status

To stop/restart area detector use ::

    user2bmb@pg10ge$ ~/2bmbOryx.sh start
    user2bmb@pg10ge$ ~/2bmbOryx.sh stop


To power cycle the camera unplug the power supply:

.. image:: ../img/camera.png 
   :width: 240px
   :align: center
   :alt: tomo_user


If the area detector fails to boot even after a camera power cycle, it means that the last auto save file (auto_setting.sav) is corrupted. To recover you need to replace the corrupted auto save with a good one::


    user2bmb@pg10ge$ ~/2bmbOryx.sh stop
    user2bmb@pg10ge$ cd ~/iocSpinnaker/autosave/
    user2bmb@pg10ge$ cp auto_settings.sav_good auto_settings.sav

then restart areadetector with::

    user2bmb@pg10ge$ ~/2bmbOryx.sh start
