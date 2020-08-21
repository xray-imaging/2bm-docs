Detector
========

Select the detector to use from:

.. image:: ../img/item_002.png 
   :width: 128px
   :align: center
   :alt: tomo_user

For FLIR Oryx the control screen is:

.. image:: ../img/item_003.png 
   :width: 720px
   :align: center
   :alt: tomo_user

To collect an image press Acquire Start.

Startup
-------

FLIR
~~~~

.. contents:: 
   :local:

To start/stop the area detector IOC for the FLIR Oryx (model 10GS 51S5) camera login into user2bmb@pg10ge then type::

    [user2bmb@pg10ge]$ ~/2bmbOryx.sh -h
    Usage: 2bmbOryx.sh {start|stop|restart|status|console|run|medm}

*FLIR Manuals*

`Installation Guide <https://anl.box.com/s/7pe793z5x9cspayqimscavzqhdcc9og7>`_
`Technical Reference <https://anl.box.com/s/iyysb20lkr9uwbbefy3s0n2pkq3lyktq>`_

List
----

.. contents:: 
   :local:

These are the model/part number of the detectors in use at 2-BM. 

.. _camera_00001:  https://www.ptgrey.com/grasshopper3-91mp-mono-usb3-vision-sony-icx814-camera        
.. _camera_00002:  https://www.ptgrey.com/grasshopper3-23-mp-mono-usb3-vision-sony-pregius-imx174-camera        
.. _camera_00003:  https://www.ptgrey.com/grasshopper3-50-mp-mono-usb3-vision-sony-pregius-imx250         
.. _camera_00004:  http://www.pco.de/fileadmin/user_upload/pco-product_sheets/pco.dimax_hs_data_sheet.pdf       
.. _camera_00005:  https://www.pco.de/scmos-cameras/pcoedge-42/       
.. _camera_00006:  http://www.adimec.com/en/Service_Menu/Industrial_camera_products/High_performance_cameras_for_the_machine_vision_applications/QUARTZ_series_High_speed_CMOS_global_shutter_cameras/Quartz_quad_CoaXPress_12_Megapixels_187fps   
.. _camera_00007:  https://www.ptgrey.com/oryx-50-mp-mono-10gige-sony-imx250         

.. |d00001| image:: ../img/dimax_01.png
   :width: 20px
   :alt: dimax_01


.. |d00002| image:: ../img/dimax_02.png
   :width: 20px
   :alt: dimax_02


.. |d00003| image:: ../img/dimax_03.png
   :width: 20px
   :alt: dimax_03


.. |d00004| image:: ../img/flir.png
   :width: 20px
   :alt: flir


+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+
|                   Detector                                  | Part number      |      pixels (HxV)     |   bit   | fps |      Manual               |               Images                           |
+=============================================================+==================+=======================+=========+=====+===========================+================================================+
| Grasshopper3 9.1 MP Mono USB3 Vision (Sony ICX814)          | GS3-U3-91S6M-C   |      3376 x 2704      | 14 none | 9   |     camera_00001_         |                                                |
+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+
| Grasshopper3 2.3 MP Mono USB3 Vision (Sony Pregius IMX174)  | GS3-U3-23S6M-C   |      1920 x 1200      | 10 none | 163 |     camera_00002_         |                                                |
+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+
| Grasshopper3 5.0 MP Mono USB3 Vision (Sony Pregius IMX250)  | GS3-U3-51S5M-C   |      2448 x 2048      | 10 none | 75  |     camera_00003_         |                                                |
+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+
| PCO DIMAX                                                   | HS4 camera link  |      2000 x 2000      | 12 2277 | 100 |     camera_00004_         |          |d00001| |d00002| |d00003|            |
+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+
| PCO EDGE                                                    | camera link      |      2048 x 2048      | 16 none | 100 |     camera_00005_         |                                                |
+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+
| Adimec                                                      | coaXPress        |      4000 x 3000      | 8  none | 187 |     camera_00006_         |                                                |
+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+
| Oryx 5.0 MP Mono 10GigE                                     | ORX-10G-51S5M-C  |      2448 x 2048      | 8-12    | 162 |     camera_00007_         |                   |d00004|                     |
+-------------------------------------------------------------+------------------+-----------------------+---------+-----+---------------------------+------------------------------------------------+


