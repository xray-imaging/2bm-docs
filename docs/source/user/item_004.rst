Sample Alignment
================

.. contents:: 
   :local:

| In order to align the sample on the center of rotation of the rotary stage 4 motorized axis are needed:
|
| • **Sample Y** (vertical motion)
| • **Sample X** (horizontal motion perpendicular to the beam)
| • **Sample top X** (horizontal motion above the rotary stage)
| • **Sample top Z** (horizontal motion normal to "sample top X" above the rotary stage)


.. image:: ../img/tomo_refs.png 
   :width: 480px
   :align: center
   :alt: tomo_user

Load the sample on the kinematic mount then using:

.. image:: ../img/tomo_admin.png 
   :width: 720px
   :align: center
   :alt: tomo_user


move the sample up/down by adjusting Tomo_Sam_Y in the positive/negative direction until the sample is in the field of view of detector. To center the sample on the rotation axis move the rotary stage Tomo_Rot at 0\ :sup:`o` then by adjusting the motor called "Tomo@0deg" (which is the sample stage on top of the rotary stage moving in the X director when the rotary stage at 0\ :sup:`o`) move the sample towards the center of the field of view. Finally move the Tomo_Rot at 180\ :sup:`o` then by adjusting the motor called "Tomo@1800deg" (which is the sample stage on top of the rotary stage moving in the X director when the rotary stage at 180\ :sup:`o`) move again the sample towards the center. The same process is described in the 4 steps below:

.. image:: ../img/sample_alignment.png
   :width: 1200px
   :align: center
   :alt: project

| **Note**: "Tomo_Sam_X" is used to align the center of rotation in respect to the beam, not to align samples on the rotation axis. While moving the sample vertically with Tomo_Sam_Y, some parasitic motions might detune "Tomo_Sam_X" by few μm. Therefore, it is expected to realign Tomo_Sam_X from one sample to another but only within few μm range.

