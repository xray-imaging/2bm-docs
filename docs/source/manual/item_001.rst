Data Analysis
=============

The raw data are automatically copied from the detector to the analysis computer (one of the tomo cluster computers) under the folder /data2/YYYY-MM/YYYY-MM-PI_lastName.

Manual
~~~~~~

To manually reconstruct a data set, use the `tomocupy cli tool <https://github.com/tomography/tomocupy-cli>`_.
::

    [2bmb@tomo4,~]$ bash
    [2bmb@tomo4,~]$ conda activate tomocupy

then for help::

    [2bmb@tomo4,~]$ tomocupy recon -h

To do a test reconstruction type::

    [2bmb@tomo4,~]$ tomocupy recon --file-name /local/data/YYYY-MM/PI_lastName/file.h5


Automatic
~~~~~~~~~

To setup a reconstruction to start and publish automatically the results on a google slide with `tomolog <https://tomologcli.readthedocs.io/en/latest/index.html>`_,
edit tomorec_log

::

    [2bmb@tomo4,~]$ bash
    [2bmb@tomo4,~]$ conda activate tomocupy
    (tomocupy) [2bmb@tomo4,~]$ sublime ~/bin/tomorec_log

by updating the --presentation-url to match the new google slide url

::

    #!/usr/bin/bash
    tomocupy recon --file-name $1 --remove-stripe-method fw --reconstruction-type full --rotation-axis-auto auto --find-center-end-row 1500
    tomolog run --presentation-url https://docs.google.com/presentation/d/1YuxMttfW8w2sfwbaw634R3_LgPIsaHblz4Lrsjzn6ufQ/edit?usp=sharing --file-name $1 --beamline 2-bm --zoom [1,2,4]

then type::

    (tomocupy) [2bmb@tomo4,~]$ auto_rec /local/data/YYYY-MM/PI_lastName/

any new raw dataset uploade in /local/data/YYYY-MM/PI_lastName/ will be automatically reconstructed and results will be published on a google slide using `tomolog <https://tomologcli.readthedocs.io/en/latest/index.html>`_.


.. image:: ../pre_apsu/img/tomolog_01.png
   :width: 512px
   :align: center
   :alt: tomo_01


At your home institution
------------------------

See the `tomocupy documentation <https://tomocupy.readthedocs.io/en/latest/>`_ for installation and usage instructions.


Mosaic
------

For samples larger than the field of view we collect multiple data sets consisiting of overlapping tiles to form a mosaic.
To reconstruct these type of data please use `tile <https://tile.readthedocs.io/en/latest/>`_  command-line-interface for mosaic tomography data processing.
