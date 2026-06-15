============================================
Move sample out of beam
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``sample_out_of_beam`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.


Name
----

``move_sample_out_of_beam``


Devices
-------

- :doc:`../manual/item_020`: **Sample stack** — the relevant lateral
  axis depends on the sample mount. Typically one of
  ``SampleTop_X`` (``2bmb:m18``), ``SampleTop_Z`` (``2bmb:m17``),
  the hexapod X/Y axes (``2bmHXP:m1 / m2``), or the sample optical
  table Y (``2bmb:m24``).


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).


Parameters
----------

- ``out_of_beam_position`` (per-axis, mm) — TBD.
- ``axis`` (which sample-stack axis to move) — TBD; depends on
  current mount.


Steps
-----

- TBD. Drive selected sample-stack axis to out-of-beam position;
  wait for DMOV.


Postconditions
--------------

:Satisfies: ``sample_out_of_beam``
:Predicate: TBD — depends on which axis was moved. Typically a
   per-axis ``.RBV`` test against the out-of-beam position with
   tolerance.


Failure modes
-------------

- Sample mount has not been characterised for "out of beam" —
  operator must define the position manually.
- Motor fault on the chosen axis.
