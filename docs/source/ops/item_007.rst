Continuous rotation
===================

You can set multiple rotation tomographic data sets to be collected continuously by simply setting a larger number of angles 
in the  `tomoScan control screen <https://tomoscan.readthedocs.io/en/latest/tomoScanApp.html#medm-files>`_. For example to collect 100 (0-180) data sets 
with 1,500 projections each you just need to set the number of projections to 150,000 and angle step to 0.12. The resulting stop angle will be 17,999.880 degree.

.. warning:: because of variable type and memory allocation in the hardware controlling the fly scan there is a limitations on the maximum number of angles (< 400,000). Also the angle step must be selected so that the resulting stop angle is less than 64,000 degree (177.7 full rotation).

