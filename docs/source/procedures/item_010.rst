============================================
Configure microscope for alignment
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``microscope_configured`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.


Name
----

``configure_microscope_for_alignment``


Source
------

Not yet implemented. Future location: `procedures/configure_microscope_for_alignment.py
<https://github.com/decarlof/2bm-procedures/blob/main/procedures/configure_microscope_for_alignment.py>`__
in the `2bm-procedures
<https://github.com/decarlof/2bm-procedures>`__ repository.


Devices
-------

- :doc:`../manual/item_020`: **MCTOptics** — drives lens / camera
  selection via ``2bm:MCTOptics:LensSelect`` and
  ``2bm:MCTOptics:CameraSelect``.
- :doc:`../manual/item_020`: **Detector optical table** —
  ``2bmb:table3``; the ``.Y`` translation axis sets the table
  vertical position so the X-ray beam lands on the camera centre.
- :doc:`../manual/item_020`: **PropagationDistance** —
  ``2bmbAERO:m1`` (the sample-to-detector Z stage; was named
  "Optique Peter Z stage" / ``Focus`` previously). Pre-positioned
  to a safe mid-band Z before the alignment procedure runs.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- :doc:`item_005` (``set_energy_to_preselect``) — beam Y at detector
  depends on energy (via DMM offset).


Parameters
----------

- ``lens_index`` (integer 0–2) — MCTOptics lens slot. Default for
  ``detector_z_rail_alignment``: 0 (1.1× wide-field lens).
- ``detector_table_y_mm`` (number) — table Y to centre the beam on
  the camera. Energy-dependent; default TBD.
- ``z_park_mm`` (number) — initial Z position for the Optique Peter
  rail. Default: 300 mm (mid of the 200–500 mm safety band used by
  :doc:`item_002`).


Steps
-----

- TBD. Set MCTOptics LensSelect; wait for LensSelected to match.
  Move ``2bmb:table3.Y`` to target; wait for jacks.
  Move ``2bmbAERO:m1`` to z_park; wait for DMOV.


Postconditions
--------------

:Satisfies: ``microscope_configured``
:Predicate: ``2bm:MCTOptics:LensSelect == lens_index`` AND
   ``2bmb:table3.Y`` within tolerance of ``detector_table_y_mm`` AND
   ``2bmbAERO:m1.RBV`` within tolerance of ``z_park_mm``.


Failure modes
-------------

- TBD.


Notes
-----

In ``detector_z_rail_alignment`` (v0.0.1) the procedure does NOT
modify lens / camera selection — it reads whatever MCTOptics is
currently set to and adapts. This precondition exists for cora's
dependency graph: it captures the assumption that the operator
(or this procedure) has already put MCTOptics into a known state
before the Z-rail alignment runs.
