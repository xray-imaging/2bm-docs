============================================
Move flag into beam (mode-dependent)
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``flag_in_beam`` of :doc:`item_002`
   (``detector_z_rail_alignment``). The flag is now in the
   :doc:`../manual/item_020` inventory (as ``2bma:m44``); the
   procedure that orchestrates it is what's still TBD.


Name
----

``set_flag_in``


Source
------

Not yet implemented. Future location: `procedures/set_flag_in.py
<https://github.com/decarlof/2bm-procedures/blob/main/procedures/set_flag_in.py>`__
in the `2bm-procedures
<https://github.com/decarlof/2bm-procedures>`__ repository.


Devices
-------

- :doc:`../manual/item_020`: **Flag (diagnostic phosphor)** —
  single vertical (Y) motor ``2bma:m44``. User/dial offset:
  ``user = dial - 5`` mm. User-coord limits: -4.5 to +35.0 mm.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- For mono mode: :doc:`item_005`
  (``set_energy_to_preselect``) — the target flag Y depends on
  the current DMM energy via the ``energy_move_flag`` lookup in
  `energy2bm.json
  <https://github.com/xray-imaging/energy/blob/main/src/energy/data/energy2bm.json>`__.


Parameters
----------

- ``mode`` (enum: ``pink`` | ``mono``) — beamline mode. Default:
  derived from current DMM state (out = pink, in = mono).
- ``flag_y_mm`` (number, user coord) — explicit target Y. If
  not given, derive from ``mode``:

  - ``pink``: ``flag_y_mm = 0`` (lower position, out of beam).
  - ``mono``: ``flag_y_mm = energy_move_flag[current_energy_keV]``
    (read from `energy2bm.json
    <https://github.com/xray-imaging/energy/blob/main/src/energy/data/energy2bm.json>`__).
    Indicative values: 13.374 keV → 23 mm, 18 keV → 17 mm,
    20 keV → 15 mm, 25 keV → 12 mm, 30+ keV → 0 mm.


Steps
-----

1. Determine target Y from ``mode`` + (if mono) energy lookup.
2. Range check: ``-4.5 ≤ flag_y_mm ≤ 35.0``.
3. ``move_motor 2bma:m44 <flag_y_mm>``; wait for DMOV.


Postconditions
--------------

:Satisfies: ``flag_in_beam``
:Predicate: ``2bma:m44.RBV`` within motor tolerance of the
   commanded ``flag_y_mm``.


Failure modes
-------------

- Motor fault on ``2bma:m44`` — check the motor record's status
  and clear before retrying.
- For mono mode: `energy2bm.json
  <https://github.com/xray-imaging/energy/blob/main/src/energy/data/energy2bm.json>`__
  has no entry for the current DMM energy — operator must either
  calibrate the lookup or pass ``flag_y_mm`` explicitly.
- Range check fails — typically operator misread mm vs encoded
  units; verify against the ``meters_all.adl`` MEDM screen.


Notes
-----

In ``detector_z_rail_alignment`` (v0.0.1) the procedure assumes
the flag is already in the appropriate position for the running
mode. If pink-beam alignment is being done, the operator should
have driven ``2bma:m44`` to 0 mm beforehand. The
``set_flag_in`` procedure formalises that step so cora's
dependency graph can resolve it.
