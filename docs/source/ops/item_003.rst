Sphere
======

The tomography scan python library `2bm tomo <https://github.com/xray-imaging/2bm-tomo>`_ contains useful functions to perform several automated tasks when used in combination with a 0.5 mm tungsten sphere as a sample. 

These funtions include automatic finding of:

- detector pixel size
- scintillator focus location
- rotation axis location
- centering of the sample on the rotation axis
- rotation axis pitch and roll


To used these methods install the  0.5 mm `tungsten sphere <https://www.vxb.com/0-5mm-Tungsten-Carbide-One-0-0197-inch-Dia-p/0-5mmtungstenballs.htm>`_ 
on the rotation axis making sure is in the field of view when the rotation axis is at 0 and 10 degrees then::

   user2bmb@pg10ge $ tomo adjust --resolution 
   user2bmb@pg10ge $ tomo adjust --focus 
   user2bmb@pg10ge $ tomo adjust --center --ask
   user2bmb@pg10ge $ tomo adjust --pitch 
   user2bmb@pg10ge $ tomo adjust --roll 

the optional **--ask** is for confirmation before moving the rotation axis location. 

