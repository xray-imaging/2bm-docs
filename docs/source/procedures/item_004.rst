============================================
Set A-station slit aperture
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``a_slits_open`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.


Name
----

``set_a_slits``


Devices
-------

- :doc:`../manual/item_020`: **A-station slits** — the upstream
  beam-shaping slits whose downstream propagation produces the
  ~1 × 1 mm beam at sample / detector. Blade motors and composite
  Size/Centre PVs documented in item_020's A-station section.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).


Parameters
----------

- ``size_h_mm`` (number > 0) — horizontal aperture. Default for
  ``detector_z_rail_alignment``: 0.5 mm (so that propagation to the
  detector produces ≈ 1 × 1 mm at the sample / detector plane).
- ``size_v_mm`` (number > 0) — vertical aperture. Default: 0.5 mm.
- ``center_h_mm`` / ``center_v_mm`` (optional) — re-centring.


Steps
-----

- TBD. Drive blade motors or write composite Size PVs; wait for
  DMOV.


Postconditions
--------------

:Satisfies: ``a_slits_open``
:Predicate: ``A_slit_H_size_RBV >= 0.5`` AND
   ``A_slit_V_size_RBV >= 0.5`` (PV names TBD —
   see item_020's A-station block).


Failure modes
-------------

- TBD.
