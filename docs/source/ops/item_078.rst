Poster
======

The create an animated poster that runs on a TV monitor using its USB input:

#. In the presentation, set that your video as always playing in the presenting mode. Open the slide in presenting mode and record the region containing your slide. On mac recording is Cmd+Shift+5. This will create a file video.mov. One can also cut video by adding options -s, -to, e.g. -ss 0:00:01 -to 0:05:04, change the frame rate, or crop it, see `ffmpeg manual <https://ffmpeg.org/ffmpeg.html>`_ for details.


#. Convert video.mov to mp4 with setting codec h264, rotating by 90°, and scaling to 1920x1080 using ffmpeg (can be installed through anaconda with ``conda install ffmpeg`` or `downloading the executables <https://ffmpeg.org/download.html>`_): ::

    ffmpeg -i video.mov -vf “transpose=1,scale=1920:1080” -vcodec h264 poster_rot_1920x1080.mp4

#. Copy the mp4 file to usb memory stick, plug it into the TV, and open the native TV player.


.. |00100| image:: ../img/poster_01.png 
    :width: 20pt

.. _video_01: https://anl.box.com/s/245ibsd1kd7mr6l21ugmimmrqo0l6uk6
.. _pptx_01: https://anl.box.com/s/752agswhjpvt3d0k0rpp9iiezwpc7ah9

+--------------------+-------------------+----------+----------------+
|  Current Posters   |   Video download  |   Image  |     PowerPoint |
+====================+===================+==========+================+
|  2-BM MICRO CT     |   video_01_       |  |00100| |    pptx_01_    |
+--------------------+-------------------+----------+----------------+

