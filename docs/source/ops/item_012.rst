==================
Beamline Alignment
==================

White Beam
==========

The first alignment step is to ensure the source white beam is centered on the beamline (50 mm × 3 mm) (H × V) fixed mask. To do this, start the detector in 2-BM-A:

::

    (base) 2bmb@lyra ~ $ 2bmbOryx5MP medm
    (base) 2bmb@lyra ~ $ 2bmbOryx5MP run

.. figure:: ../img/beamline_alignment_001.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_001

   2-BM-A areadetector control screen

Start ImageJ: 

::

    (base) 2bmb@lyra ~ $ ImageJ

and configure the EPICS_NTNDA plug-in:

.. figure:: ../img/beamline_alignment_002.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_002

   ImageJ EPICS_NTNDA plug-in

Lower M1 by setting Yaverage to -2 mm and Angle to 0 mrad. 

.. figure:: ../img/beamline_alignment_003.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_003

   2-BM-A M1 mirror control screen

Lower the DMM by setting its three vertical stages (USY-OB, USY-IB, and DSY) to -19 mm.

.. figure:: ../img/beamline_alignment_004.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_004

   DMM control screen

Adjust the camera vertical position (2bma:m21) to see the white beam:

.. figure:: ../img/beamline_alignment_005.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_005

   White beam at 2-BM-A with 1 mm Al filter -- Exposure 0.004 s 20 mm glass filter

Remove the 1 mm Al filter.

.. figure:: ../img/beamline_alignment_006.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_006

   White beam at 2-BM-A without any filter -- Exposure 0.004 s 20 mm glass filter

Plot a vertical line to show the white beam intesity

.. figure:: ../img/beamline_alignment_007.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_007

   White beam vertical intesity plot

If the plot is not symmetric `ask the control room <https://ops.aps.anl.gov/Internal/Reference/Test2/instructions.html>`_ to steer the beam in 10 µrad steps.

Insert the mirror by setting Yaverage to 0 mm and Angle to 0 mrad.

Recalibrate the mirror Yaverage and angle by adjusting:

#. Yaverage until the mirror cuts the white beam image in half.
#. The mirror angle until there is no reflection.

Once steps 1 and 2 are completed, reset the mirror Yaverage and angle to zero.

.. warning:: To better visualize the reflected beam, enable the Proc1 plugin. Then, in the Flat Field Normalization section, click Save Flat Field and Enable Flat Field.

.. figure:: ../img/AD_proc1.png
   :width: 720px
   :align: center
   :alt: AD_proc1



Pink beam
=========

Adjust the mirror angle to 2.618 mrad (0.15°) and move the camera up until you see the pink beam.

.. figure:: ../img/beamline_alignment_008.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_008

   Pink beam after steering -- Exposure 0.004 s 20 mm glass filter

Adjust the camera vertical position until the image of the pink beam is centered, and set the camera Y position to 0.

Mono beam
=========

Set the DMM's three vertical stages (USY-OB, USY-IB, and DSY) to 0 mm.
Set the DMM Upstream arm to 0°.

.. figure:: ../img/beamline_alignment_009.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_009

   Pink beam cut in half by the first DMM crystal

Recalibrate the DMM table height and the first crystal angle by adjusting:

#. The three vertical stages (USY-OB, USY-IB, and DSY) until the first crystal cuts the pink beam image in half.
#. The first crystal angle (DMM Upstream arm) until there is no reflection.

Once steps 1 and 2 are completed, reset USY-OB, USY-IB, DSY, and the DMM Upstream arm angle to zero.

To recalibrate the second crystal angle:

#. Move the DMM vertical stages (USY-OB, USY-IB, and DSY) down by 10 mm.
#. Move DMM M2Y down until the second crystal cuts the pink beam in half.
#. Adjust the second crystal angle until there is no reflection.

.. figure:: ../img/beamline_alignment_010.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_010

   Pink beam cut in half by the second DMM crystal

Once steps 1, 2, and 3 are completed, reset DMM M2Y to 10 mm and the second crystal angle (DMM Downstream arm) to 0.

To find the DMM monochromatic beam, move the DMM into the beam (set USY-OB, USY-IB, and DSY to 0) and set the DMM Upstream arm to 1.25°. The distance between the centers of the first and second crystal is approximately 600 mm.

:math:`\tan(2 * 1.25) \times 600 = 26.196 mm`

#. Move DMM M2Y to 26.196 mm.
#. Move the detector Y (2bma:m21) to 26.196 mm.

.. figure:: ../img/beamline_alignment_011.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_011

   DMM in position

Adjust the detector Y (2bma:m21) until you see the DMM monochromatic beam:

.. figure:: ../img/beamline_alignment_012.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_012

   DMM monochromatic beam

Maximize intensity and size by adjusting only the DMM Downstream arm and DMM M2Y.

.. figure:: ../img/beamline_alignment_013.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_013

   Optimized DMM monochromatic beam 

.. figure:: ../img/beamline_alignment_014.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_014

   DMM position after monochromatic beam optimization 


Reset the second crystal angle (DMM Downstream arm) to 1.25°.

.. figure:: ../img/beamline_alignment_015.png
   :width: 720px
   :align: center
   :alt: beamline_alignment_015

   DMM position after monochromatic beam optimization and second crystal position reset

Since the optimal DMM M2Y is at 26.046 mm instead of the calculated 26.196 mm, the correct distance between the centers of the first and second crystal is 596.56 mm.

See the `Energy calibration <https://docs2bm.readthedocs.io/en/latest/source/ops/item_022.html>`_ section to correctly associate the DMM arm angles with the corresponding energy.
