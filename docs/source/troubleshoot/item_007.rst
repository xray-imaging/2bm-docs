Tomo@0deg and Tomo@90deg
========================

.. contents:: 
   :local:


The Tomo@0deg and Tomo@90deg motors are not responding (control screes is white)


These two motors are controlled by an EPICS softIOC. If the screen for Tomo@0deg and Tomo@90deg
are white it means that the softIOC was not started. To solve this please run::

    $ start_2bmS1.sh
