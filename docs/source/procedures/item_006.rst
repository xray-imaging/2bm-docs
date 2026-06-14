============================================
Move flag into beam
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``flag_in_beam`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.

   The **flag** is a downstream element scheduled to be added to
   the 2-BM beamline. It is not yet present in
   :doc:`../manual/item_020` — this stub records the precondition
   slot so that the procedure graph for ``detector_z_rail_alignment``
   has a target to point at. Replace the placeholders below once the
   flag is installed.


Name
----

``set_flag_in``


Devices
-------

- :doc:`../manual/item_020`: **Flag** (TBD — not yet in the
  hardware inventory). Single-axis Y motion.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).


Parameters
----------

- ``flag_y_mm`` (number) — Y position for in-beam state. Default:
  TBD.


Steps
-----

- TBD. Move flag Y motor to in-beam position; wait for DMOV.


Postconditions
--------------

:Satisfies: ``flag_in_beam``
:Predicate: ``Flag_Y_RBV`` within tolerance of ``flag_y_mm``
   (PV name TBD).


Failure modes
-------------

- TBD.
