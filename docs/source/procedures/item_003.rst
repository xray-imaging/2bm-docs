============================================
Enable beamline for beam
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``beamline_enabled`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.


Name
----

``enable_beamline``


Devices
-------

- :doc:`../manual/item_020`: **BLEPS** — beamline equipment
  protection system, prerequisite to admit beam.
- :doc:`../manual/item_020`: **A-shutter (front-end)** —
  ``S02BM-PSS:FES`` (the procedure may open this, or rely on
  :doc:`item_007` for the B-shutter side).
- 2-BM PSS — personnel safety system, hutch search state.
- APS machine ops — beam in storage ring.


Preconditions
-------------

- TBD. Likely includes: hutches searched and locked, BLEPS clear of
  Fault, APS top-up in progress, no scheduled beam dump.


Parameters
----------

- TBD.


Steps
-----

- TBD.


Postconditions
--------------

:Satisfies: ``beamline_enabled``
:Predicate: TBD — no single PV today; depends on BLEPS status
   register + PSS hutch status + machine-state PV. Likely requires
   a composite predicate.


Failure modes
-------------

- TBD.


Notes
-----

This is a high-level orchestration procedure that itself probably
breaks down into sub-procedures (clear BLEPS faults, search hutch,
open FES, etc.). The intent of this stub is to give the precondition
graph of :doc:`item_002` a target to point at; the real procedure
likely lives at the floor-coordinator / OBS-shift level.
