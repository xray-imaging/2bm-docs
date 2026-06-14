============================================
Open B-shutter (P6-50 Safety Shutter)
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``b_shutter_open`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.


Name
----

``open_b_shutter``


Devices
-------

- :doc:`../manual/item_020`: **P6-50 Safety Shutter (B-shutter)** —
  ``S02BM-PSS:SBS``. Open command ``OpenEPICSC``; close command
  ``CloseEPICSC``; status readback ``S02BM-PSS:SBS:BeamBlockingM``.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``) — PSS must permit the open.


Parameters
----------

- (none)


Steps
-----

1. ``caput S02BM-PSS:SBS:OpenEPICSC 1``.
2. Wait for ``S02BM-PSS:SBS:BeamBlockingM`` to read ``OFF``
   (STATE 0; **inverted enum** — OFF means NOT blocking → shutter
   OPEN). See :doc:`../manual/item_020` for the semantic note.


Postconditions
--------------

:Satisfies: ``b_shutter_open``
:Predicate: ``S02BM-PSS:SBS:BeamBlockingM == 0`` (i.e. ``OFF``).


Failure modes
-------------

- ``BeamBlockingM`` does not transition to ``OFF`` within the
  timeout — PSS permit not granted, hardware fault, or BLEPS
  Fault latched. Verify upstream interlocks; do not retry blindly.
