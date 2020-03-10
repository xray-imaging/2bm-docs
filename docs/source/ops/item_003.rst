Sphere
======

The tomography scan python library `2bm tomo <https://github.com/xray-imaging/2bm-tomo>`_ contains useful functions to perform several automated tasks when used in combination with a 0.5 mm tungsten sphere as a sample. 

These funtions include:

- determination of the detector pixel size
- determination of the rotation axis location
- ...


To used these methods install the  0.5 mm tungsten sphere on the rotation axis making sure is in the field of view when the rotation axis is at 0 and 10 degrees then::


   [user2bmb@pg10ge]$ tomo find --option resolution
   [user2bmb@pg10ge]$ tomo find --option center

