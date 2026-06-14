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
:Predicate: Composite, all concrete:

   - ``S02BM-PSS:StaA:SecureM == 1`` (``ON`` — 2-BM-A hutch
     searched and locked; see :doc:`../manual/item_020`).
   - ``S02BM-PSS:StaB:SecureM == 1`` (``ON`` — 2-BM-B hutch
     searched and locked).
   - ``S02BM-PSS:FES:BeamBlockingM == 0`` (``OFF`` — FES open;
     same inverted-enum convention as the other shutter status
     PVs in item_020).
   - ``SR-ACIS:2BM:FesPermitM == 1`` (``ON`` — ACIS upstream
     permit. Aggregates storage-ring health, injection state,
     APS-wide permits, and the BLEPS fault chain into one
     boolean. See item_020's ACIS upstream-permit block).

   The ACIS permit replaces what was previously a separate
   "BLEPS-clear" and "APS-machine-state" pair of TBDs — ACIS
   composes both upstream.


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
