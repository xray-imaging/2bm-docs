============================================
Set energy to preselect
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``energy_configured`` of :doc:`item_002`
   (``detector_z_rail_alignment``). To be fleshed out as the
   procedure is implemented.


Name
----

``set_energy_to_preselect``


Source
------

Not yet implemented in this repository. Future location: `procedures/set_energy_to_preselect.py
<https://github.com/decarlof/2bm-procedures/blob/main/procedures/set_energy_to_preselect.py>`__
in the `2bm-procedures
<https://github.com/decarlof/2bm-procedures>`__ repository. Likely a
thin wrapper around the `energy
<https://github.com/xray-imaging/energy>`__ package, which already
implements the coordinated DMM + mirror energy change.


Devices
-------

- :doc:`../manual/item_020`: **Mirror M1** and **DMM** — drives both
  optics to the energy-dependent positions in a coordinated move.
- The reference implementation lives in the ``energy`` package at
  https://github.com/xray-imaging/energy. The mono-energy
  lookup table is at
  https://github.com/xray-imaging/energy/blob/main/src/energy/data/energy2bm.json
  (used by :doc:`item_006` for the energy-dependent flag Y and by
  :doc:`item_008` for the energy-dependent B-slit Y).


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- :doc:`item_007` (``open_b_shutter``) — beam must reach the optics
  to verify the energy change.


Parameters
----------

- ``energy_keV`` (number > 0) — target energy. Default for
  ``detector_z_rail_alignment``: TBD (whichever preselect the
  experiment is running on).


Steps
-----

- TBD. Delegate to the ``energy`` package
  (``energy.change_energy(keV)``); wait for mirror + DMM motors to
  settle.


Postconditions
--------------

:Satisfies: ``energy_configured``
:Predicate: Mirror M1 and DMM RBVs match the energy-dependent
   lookup tables in the ``energy`` package (predicate is
   energy-parameterised — needs current energy as input).


Failure modes
-------------

- TBD.
