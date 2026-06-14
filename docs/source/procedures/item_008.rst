============================================
Set B-station slits for alignment
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``b_slits_configured`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.


Name
----

``set_b_slits``


Devices
-------

- :doc:`../manual/item_020`: **B-station slits** — blade motors
  ``2bma:m9 / m10`` (vertical pair) and ``2bma:m11 / m12``
  (horizontal pair). Composite Size / Centre PVs may not be
  published; drive blades directly and derive size = blade₁ − blade₂
  and centre = (blade₁ + blade₂) / 2.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- :doc:`item_005` (``set_energy_to_preselect``) — the slit-centre Y
  is energy-dependent (DMM offsets the beam vertically).


Parameters
----------

- ``size_h_mm`` (number > 0) — horizontal aperture. Default for
  ``detector_z_rail_alignment``: 1.0 mm.
- ``size_v_mm`` (number > 0) — vertical aperture. Default: 1.0 mm.
- ``center_v_mm`` (number) — vertical centre, energy-dependent.
  Read from energy-package lookup. Default: TBD.
- ``center_h_mm`` (number, optional) — horizontal centre. Default:
  current value (don't re-centre).


Steps
-----

- TBD. Compute blade targets from size + centre; drive each blade;
  wait for all four DMOV.


Postconditions
--------------

:Satisfies: ``b_slits_configured``
:Predicate: ``|2bma:m12 - 2bma:m11 - size_h| < ε``,
   ``|2bma:m9 - 2bma:m10 - size_v| < ε``, centre PVs likewise
   within tolerance.


Failure modes
-------------

- One or more blades does not reach target — check motor faults on
  ``2bma:m9–m12`` motor records.
