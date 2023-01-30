Continuous rotation
===================

You can set multiple rotation tomographic data sets to be collected continuously by simply setting a larger number of angles 
in the  `tomoScan control screen <https://tomoscan.readthedocs.io/en/latest/tomoScanApp.html#medm-files>`_. For example to collect 100 (0-180) data sets 
with 1,500 projections each you just need to set the number of projections to 150,000 and angle step to 0.12. The resulting stop angle will be 17,999.880 degree.

.. warning:: because of variable type and memory allocation in the hardware controlling the fly scan there is a limitation on the maximum number of angular positions at which you can trigger the collection of an image (this is set at 2-BM-A at 400,000). Also, the angle step must be selected so that the resulting stop angle is less than 65,283 degree (181 full rotations). This is because the controller can only count +/- 2^31 encoder counts from the zero point and with the ABR250 a range of +/- 2^31-1 encoder pulses at 0.0000304 deg/pulse corresponds to +/- 65,283 deg.

