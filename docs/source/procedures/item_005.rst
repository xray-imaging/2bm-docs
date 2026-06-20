============================================
Set energy to preselect
============================================

.. warning::

   **STATUS: STUB.** Placeholder for the procedure that satisfies
   the precondition ``energy_configured`` of :doc:`item_002`
   (``detector_z_rail_alignment``) and is the natural target of
   cora's eventual ``BeamEnergy`` Method surface. To be fleshed
   out as the procedure is implemented in
   `2bm-procedures <https://github.com/decarlof/2bm-procedures>`__.

   The two-path Steps below capture the algorithm already in the
   deployed `decarlof/energy
   <https://github.com/decarlof/energy>`__ package; the operator
   (DeCarlo) will validate the linear-interp assumption per motor
   when writing the formal procedure.


Name
----

``set_energy_to_preselect``


Source
------

Not yet implemented as its own script in this repository. Future
location: `procedures/set_energy_to_preselect.py
<https://github.com/decarlof/2bm-procedures/blob/main/procedures/set_energy_to_preselect.py>`__
in the `2bm-procedures
<https://github.com/decarlof/2bm-procedures>`__ repository. Likely
a thin wrapper around the live `decarlof/energy
<https://github.com/decarlof/energy>`__ package, which already
implements the coordinated DMM + mirror + downstream per-energy
move.

Deployed code base:
``/home/beams/2BMB/epics/synApps/support/energy/`` (git remote
`decarlof/energy <https://github.com/decarlof/energy>`__). That
fork is reconciled with the
`xray-imaging/energy <https://github.com/xray-imaging/energy>`__
upstream periodically via pull request; cite the fork URL when
referencing the live deployment.

The lookup table is at
`src/energy/data/energy2bm.json
<https://github.com/decarlof/energy/blob/main/src/energy/data/energy2bm.json>`__
(``store_0`` field per motor). The position-resolution algorithm is
in ``src/energy/util.py::interpolate()`` driven from
``src/energy/__main__.py::run_set()`` lines 63–106.


Devices
-------

- :doc:`../manual/item_020`: **Mirror M1** and **DMM** — drives both
  optics, plus the downstream tracking group (table3y, flag,
  B-hutch slits, filter paddle) to the per-energy positions in a
  coordinated move. The full driven-motor list is in the
  `Composite IOCs section of item_020.rst
  <https://docs2bm.readthedocs.io/en/latest/source/manual/item_020.html#composite-iocs>`__.
- The Mono + Pink lookup tables share the single ``energy2bm.json``
  file linked above (used by :doc:`item_006` for the energy-dependent
  flag Y, by :doc:`item_008` for the energy-dependent B-slit Y, and
  by the per-energy DMM-axis tables in the
  :doc:`../manual/item_020` DMM block).


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- :doc:`item_007` (``open_b_shutter``) — beam must reach the optics
  to verify the energy change.
- **A-shutter handling is NOT automated by the energy change.** The
  deployed ``move.py::motors()`` has both the close and re-open
  calls explicitly commented out (the close-side log still prints
  "closing A-shutter" misleadingly; the open-side log says
  "opening shutter after energy change has been disabled"). The
  A-shutter stays open continuously throughout an experimental
  session per the operational practice documented in the A-shutter
  block of :doc:`../manual/item_020` — closing and re-opening it
  per energy change would create thermal transients in the
  beamline optics. The formal procedure's precondition is simply
  that the A-shutter is in whatever state the operator has chosen
  (typically open); no per-move shutter sequencing.


Parameters
----------

- ``energy_keV`` (number > 0) — target energy. Default for
  ``detector_z_rail_alignment``: TBD (whichever preselect the
  experiment is running on).
- ``mode`` (``"Mono"`` | ``"Pink"``) — selects which lookup
  branch of ``energy2bm.json`` to use. Live calibrated set: Mono
  has 6 energies (13.374, 13.574, 18.000, 20.000, 25.000, 25.584
  keV); Pink has 4 (30.000, 40.000, 50.000, 60.000 keV).
- ``force`` (boolean, default false) — skip the interactive
  "Confirm energy change?" prompt (operator-side safety gate in
  the live implementation).


Steps
-----

**Two execution paths** depending on whether the requested energy is
in the calibrated lookup table. Both are already implemented in
``decarlof/energy``; this procedure wraps them.

**Path A — exact match (pre-calibrated energy)**

1. Round requested ``energy_keV`` to 3 decimals.
2. Look up positions in ``energy2bm.json`` under
   ``mode`` → ``"{:.3f}".format(energy_keV)``.
3. Apply each saved position to its motor — every key matching
   ``energy_move*`` (interpolatable) and ``energy_pos*`` (static).
4. Wait for ``AllDoneA`` AND ``AllDoneB`` flags to clear before
   declaring the move complete.

**Path B — interpolation (energy not in calibrated list)**

Reference algorithm (live ``util.py::interpolate()`` +
``__main__.py::run_set()`` lines 63–106):

1. Reject if requested energy is **outside** the calibrated range
   ``[min(calibrated), max(calibrated)]`` for the selected mode.
2. Find the nearest calibrated energy
   (``util.find_nearest(energies_flt, energy_select)``).
3. Identify the bracketing pair ``(energy_low, energy_high)``
   that straddles the requested value (one calibrated energy on
   each side of the request).
4. For each motor key in the bracketing pair's lookup row, **if the
   key matches** ``energy_move*``: linearly interpolate ::

      pos_new = pos_low + ((energy_select - energy_low)
                           / (energy_high - energy_low))
                          * (pos_high - pos_low)

5. Keys matching ``energy_pos*`` are **not** interpolated — these
   are static positions (currently just ``fltr1select``, the
   downstream filter-paddle slot index) that don't vary
   continuously with energy.
6. Apply the resulting interpolated dictionary the same way Path A
   applies the lookup-row dictionary.

The ``energy_move*`` vs ``energy_pos*`` distinction is encoded in
the JSON key names and honoured by both ``util.py::interpolate()``
(which filters on ``'energy_move' in key``) and ``move.py::motors()``
(which accepts both prefixes for the actual move).

.. note::

   **Pending operator validation.** Linear interpolation is the
   algorithm already deployed for off-table energies. The operator
   will validate the linear assumption **motor-by-motor** when
   writing the formal procedure in ``2bm-procedures`` — most
   energy-tracked axes change smoothly with energy and linear is a
   good first approximation, but some may need non-linear curves
   (e.g., if the Bragg-angle parametrisation introduces a
   trigonometric non-linearity that's visible at the operational
   precision).

   The step-pattern visible in the B-slit centre trajectory (see
   the docs ENERGY-2 table) is an artefact of operator save batching
   rather than a physical non-linearity that interpolation would
   need to respect.


Postconditions
--------------

:Satisfies: ``energy_configured``
:Predicate: Mirror M1 motors AND all driven DMM / downstream-tracking
   motors AND ``flag`` AND B-slit Y pair AND filter-paddle slot
   match either:

   - the exact pre-calibrated row (Path A), or
   - the linearly interpolated targets (Path B)

   within per-motor position tolerance. The energy-mode PV
   (``2bma:TomoScan:EnergyMode``) and the energy PV
   (``2bma:TomoScan:Energy``) are updated to reflect the new state.


Failure modes
-------------

- **Energy outside calibrated range** (Path B) → live code logs
  ``Error: energy selected ... is outside the calibrated range
  [min, max]`` and aborts before any motion.
- **Calibrated list is empty or corrupt** → live code logs
  ``Pre-calibrated energies file ... is missing or corrupted``
  and aborts.
- **Calibrated list contains only one entry** → no interpolation
  possible; only Path A works (exact match required). Currently
  not an operational concern (Mono has 6, Pink has 4).
- (Front-end shutter handling: not applicable — the deployed
  ``move.py::motors()`` does not close or re-open the A-shutter
  during the energy change, see Preconditions above. The A-shutter
  stays in whatever state the operator left it in.)
- **One or more motors fail to reach the commanded position**
  within timeout (``AllDoneA`` / ``AllDoneB`` never clears) →
  the energy mode PV is NOT updated and the procedure reports
  partial completion.


Operator walkthrough
--------------------

Live CLI invocation (the formal procedure will wrap this):

::

   [2bmb@arcturus]$ energy set --mode Mono --energy 20.0

(or ``--mode Pink``). The CLI handles Path A vs Path B internally
based on whether the energy is in the calibrated list. The
``--force`` flag skips the interactive Y/N prompt.

Detailed CLI usage is in :doc:`../ops/item_021`.


Notes
-----

- The ``energy2bm.json`` ``store_0`` field is the single source of
  truth for calibrated positions across all energy-tracked axes
  (DMM Bragg arms + ``M2 Y``, mirror stripe + table X in Pink mode,
  diagnostic flag Y, B-station slit vertical pair, table3y, filter
  paddle index, plus the ``m1angl`` / ``m1avg`` mirror geometry
  setpoints). Adding a calibrated energy means editing this file
  (or using ``energy add --energy N``), not the IOC source or
  this procedure.
- The deployed package is the user's fork (``decarlof/energy``)
  reconciled with ``xray-imaging/energy`` upstream periodically via
  PR. The live JSON ``store_0`` values may temporarily lag what
  ends up on the xray-imaging main branch (and vice versa).
- This procedure is the natural cora ``BeamEnergy`` Method target
  — a single operator-facing "set energy to X keV" surface that
  resolves into the ~15 coordinated motor moves catalogued in
  the :doc:`../manual/item_020` Composite IOCs section.
- The sandbox at ``/home/beams8/DECARLO/conda/sandbox/energy/``
  has three one-off scripts (TomoScan-PV wrapper, enum-PV dispatcher,
  Bragg-arm calibration scan) — useful as snippets when implementing
  the formal procedure, but not part of the deployment.
