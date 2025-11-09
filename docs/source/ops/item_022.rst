==================
Energy Calibration
==================

X-ray Energy Calibration Using Channel-Cut Crystal
==================================================

**Channel-cut crystal parameters:**

- Length: **36 mm**
- Width: **3 mm**
- Lattice spacing (2d): **3.84 Å**

Purpose
-------

To accurately calibrate the monochromator energy using the known lattice spacing of the channel-cut crystal.

Procedure
---------

1. **Mount the Channel-Cut Crystal**

- Secure the channel-cut crystal on the rotation stage in the x-ray beam path.
- Align the crystal so that the incident beam fully illuminates the 3 mm width and the reflection geometry is symmetric and set the rotation angle to zero.

.. figure:: ../img/energy_cal_00.png
   :width: 720px
   :align: center
   :alt: energy_cal_00


2. **Set Initial Conditions**

   - Set the monochromator energy to approximately **20 keV**.
   - Compute the expected Bragg angle :math:`\theta` using Bragg’s law:

     .. math::

        E = \frac{12.3984}{2d \sin\theta} \quad [\text{keV}]

     which can be rearranged as:

     .. math::

        \theta = \arcsin\!\left(\frac{12.3984}{2d\,E}\right)

     where :math:`2d = 3.84\,\text{Å}` and :math:`E = 20\,\text{keV}`.

   - For a 20 keV x-ray beam, the expected Bragg angle is approximately **9.29°**.

   Example Python code to compute the expected angle:

   .. code-block:: python

      import numpy as np

      # Parameters
      two_d = 3.84         # 2d in Å
      E_nom = 20.0         # nominal energy in keV
      hc = 12.3984         # hc in keV·Å

      # Compute Bragg angle (radians and degrees)
      theta_rad = np.arcsin(hc / (two_d * E_nom))
      theta_deg = np.degrees(theta_rad)

      print(f"Bragg angle at {E_nom} keV: {theta_deg:.6f}°")

3. **Record the reflected x-ray**

   - Rotate the crystal at the calculated Bragg angle until you find the reflection. 
   - Select an ROI around the reflection using the ROI plugin fi area detector together with the Stat2 plugin to calculate the mean value.

.. figure:: ../img/energy_cal_03.png
   :width: 512px
   :align: center
   :alt: energy_cal_03

.. figure:: ../img/energy_cal_04.png
   :width: 512px
   :align: center
   :alt: energy_cal_04


4. **Perform Rocking Curve Scan**

   - Perform a fine angular scan (rocking curve) around the calculated Bragg angle to record the reflected x-ray intensity versus angle.

 .. figure:: ../img/energy_cal_01.png
   :width: 512px
   :align: center
   :alt: energy_cal_01

.. figure:: ../img/energy_cal_02.png
   :width: 256px
   :align: center
   :alt: energy_cal_02
     

5. **Identify the Peak Position**

   - Fit the rocking curve to find the precise Bragg peak angle :math:`\theta_B`.
   - This corresponds to the true Bragg condition for that energy.

.. figure:: ../img/energy_cal_05.png
   :width: 720px
   :align: center
   :alt: energy_cal_05

You can run mdavis:

::

   (base) 2bmb@arcturus $ cd /APSshare/bin
   (base) 2bmb@arcturus $ ./mdaviz

6. **Calculate the True Energy**

   - Compute the actual energy using Bragg’s law:

     .. math::

        E = \frac{12.3984}{2d \sin\theta_B} \quad [\text{keV}]

   Example Python code to compute the true energy from the measured peak angle:

   .. code-block:: python

      import numpy as np

      # Parameters
      two_d = 3.84         # 2d in Å
      E_nom = 20.0         # nominal energy in keV
      hc = 12.3984         # hc in keV·Å

      # Example: replace this with your measured peak angle in degrees
      theta_B_deg = 9.2903
      theta_B_rad = np.radians(theta_B_deg)

      # Compute measured energy and offset
      E_meas = hc / (two_d * np.sin(theta_B_rad))
      offset_keV = E_meas - E_nom

      print(f"Measured energy: {E_meas:.6f} keV")
      print(f"Offset from nominal: {offset_keV:.6f} keV")

7. **Adjust Monochromator Calibration**

   - Compare the calculated true energy to the nominal monochromator value (20 keV).
   - Apply an energy offset correction in the control software if necessary.

8. **Verify Calibration**

   - Repeat the procedure at another energy (e.g., 19 keV or 21 keV) to confirm linearity and consistency.



Comparison of Calculated and Measured X-ray Energies
====================================================

The table below lists the calculated X-ray energies using a 24 Å W–B₄C multilayer period
(first-order Bragg reflection) and compares them with the measured energies for various
incident angles.

+------------------------+------------+--------------------+--------------------------+------------------------+
| Angle (°)              | sin(θ)     | λ (Å) = 2d·sinθ   | Calculated Energy (keV)  | Measured Energy (keV)   |
+========================+============+====================+==========================+========================+
| 1.1309999999999922     | 0.0197396  | 0.9475             | 13.09                    | 13.374                 |
+------------------------+------------+--------------------+--------------------------+------------------------+
| 1.0809999999999933     | 0.0188718  | 0.9059             | 13.68                    | 13.574                 |
+------------------------+------------+--------------------+--------------------------+------------------------+
| 0.8220000000000001     | 0.0143412  | 0.6884             | 18.02                    | 18.000                 |
+------------------------+------------+--------------------+--------------------------+------------------------+
| 0.726                  | 0.0126695  | 0.6081             | 20.39                    | 20.000                 |
+------------------------+------------+--------------------+--------------------------+------------------------+
| 0.5772499999999998     | 0.0100756  | 0.4836             | 25.63                    | 25.000                 |
+------------------------+------------+--------------------+--------------------------+------------------------+
| 0.5609999999999995     | 0.0097919  | 0.4700             | 26.38                    | 25.584                 |
+------------------------+------------+--------------------+--------------------------+------------------------+

.. note::

   Calculated energies are obtained from Bragg's law:

   .. math::

      E = \frac{12.3984193}{2 d \, \sin\theta} \;\text{[keV]}

   where d = 24 Å is the multilayer period and \theta is the incident angle.


Incident Angle for Given X-ray Energies
=======================================

The table below shows the incident angles θ (in degrees) for selected X-ray energies
assuming a 24 Å W–B₄C multilayer and first-order Bragg reflection.

+------------------+---------+
| Energy (keV)     | Angle ° |
+==================+=========+
| 13               | 1.145   |
+------------------+---------+
| 14               | 1.061   |
+------------------+---------+
| 15               | 0.990   |
+------------------+---------+
| 16               | 0.928   |
+------------------+---------+
| 17               | 0.872   |
+------------------+---------+
| 18               | 0.823   |
+------------------+---------+
| 19               | 0.779   |
+------------------+---------+
| 20               | 0.739   |
+------------------+---------+
| 21               | 0.703   |
+------------------+---------+
| 22               | 0.670   |
+------------------+---------+
| 23               | 0.640   |
+------------------+---------+
| 24               | 0.612   |
+------------------+---------+
| 25               | 0.586   |
+------------------+---------+
| 26               | 0.561   |
+------------------+---------+
| 27               | 0.538   |
+------------------+---------+
| 28               | 0.517   |
+------------------+---------+
| 29               | 0.497   |
+------------------+---------+
| 30               | 0.478   |
+------------------+---------+
| 31               | 0.460   |
+------------------+---------+
| 32               | 0.443   |
+------------------+---------+
| 33               | 0.426   |
+------------------+---------+


.. note::

   Angles are in degrees (grazing incidence). Calculated using Bragg's law:

   .. math::

      \theta = \arcsin\left(\frac{12.3984193}{2 d E}\right)

   where d = 24 Å is the multilayer period and E is the desired X-ray energy in keV.


