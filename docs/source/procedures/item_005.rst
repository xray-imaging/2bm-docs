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

   The interpolation algorithm and the two-codebase situation
   captured below are the **starting points** for the
   implementation — the operator (DeCarlo) will validate and
   refine both when writing the formal procedure.


Name
----

``set_energy_to_preselect``


Source
------

Not yet implemented in this repository. Future location:
`procedures/set_energy_to_preselect.py
<https://github.com/decarlof/2bm-procedures/blob/main/procedures/set_energy_to_preselect.py>`__
in the `2bm-procedures
<https://github.com/decarlof/2bm-procedures>`__ repository. Likely a
thin wrapper around the `energy
<https://github.com/xray-imaging/energy>`__ package, which already
implements the coordinated DMM + mirror per-energy move.

**Two divergent reference implementations exist on disk**; the formal
procedure will choose / port from these:

.. list-table::
   :header-rows: 1
   :widths: 25 50 25

   * - Codebase
     - Path
     - Interpolation?
   * - Newer ``xray-imaging/energy`` (deployed)
     - ``/home/beams8/USER2BMB/conda/energy-decarlof/src/energy/``
       (= `xray-imaging/energy <https://github.com/xray-imaging/energy>`__)
     - **No** — ``move.py::motors()`` does raw dict lookup
       (``pos = pos_energy_select[energy][key]``); KeyError if
       requested energy is not in ``energy2bm.json``.
   * - Older synApps support
     - ``/home/beams8/USER2BMB/epics/synApps/support/energy/src/energy/``
     - **Yes** — linear interpolation in
       ``util.py::interpolate()`` driven from
       ``__main__.py::run_set()`` lines 63–106.

The lookup table itself (`energy2bm.json
<https://github.com/xray-imaging/energy/blob/main/src/energy/data/energy2bm.json>`__,
``store_0`` field) is the same in both codebases; only the
position-resolution algorithm differs.


Devices
-------

- :doc:`../manual/item_020`: **Mirror M1** and **DMM** — drives both
  optics, plus the downstream tracking group (table3y, flag,
  B-hutch slits, filter paddle) to the per-energy positions in a
  coordinated move. The full driven-motor list is in the
  `Composite IOCs section of item_020.rst
  <https://docs2bm.readthedocs.io/en/latest/source/manual/item_020.html#composite-iocs>`__.
- The mono-energy + Pink-energy lookup tables are at
  https://github.com/xray-imaging/energy/blob/main/src/energy/data/energy2bm.json
  (used by :doc:`item_006` for the energy-dependent flag Y, by
  :doc:`item_008` for the energy-dependent B-slit Y, and the
  per-energy DMM-axis tables in the
  :doc:`../manual/item_020` DMM block).


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- :doc:`item_007` (``open_b_shutter``) — beam must reach the optics
  to verify the energy change.
- A-shutter is closed for the duration of the move (see the older
  synApps ``move.py::motors()`` which auto-closes / re-opens the
  front-end shutter around the motor moves).


Parameters
----------

- ``energy_keV`` (number > 0) — target energy. Default for
  ``detector_z_rail_alignment``: TBD (whichever preselect the
  experiment is running on).
- ``mode`` (``"Mono"`` | ``"Pink"``) — selects which lookup
  branch of ``energy2bm.json`` to use. Mono has 6 calibrated
  energies (13.374, 13.574, 18, 20, 25, 25.584 keV); Pink has 4
  (30, 40, 50, 60 keV).
- ``force`` (boolean, default false) — skip the interactive
  "Confirm energy change?" prompt (operator-side safety gate in
  the synApps reference implementation).


Steps
-----

**Two execution paths** depending on whether the requested energy is
in the calibrated lookup table:

**Path A — exact match (pre-calibrated energy)**

1. Round requested ``energy_keV`` to 3 decimals.
2. Look up positions in ``energy2bm.json`` for ``mode`` →
   ``"{:.3f}".format(energy_keV)``.
3. Apply each saved position to the driven motor (all keys matching
   ``energy_move*`` and ``energy_pos*`` per the lookup row).
4. Wait for ``AllDoneA`` AND ``AllDoneB`` flags to clear before
   declaring the move complete.

**Path B — interpolation (energy not in calibrated list)**

Reference algorithm (older synApps ``__main__.py::run_set`` +
``util.py::interpolate``):

1. Reject if requested energy is **outside** the calibrated range
   ``[min(calibrated), max(calibrated)]`` for the selected mode.
2. Find the nearest calibrated energy
   (``util.find_nearest(energies_flt, energy_select)``).
3. Identify the bracketing pair ``(energy_low, energy_high)``
   that straddles the requested value (one calibrated energy on
   each side of the request).
4. For each motor key in the bracketing pair's lookup row, **if the
   key matches ``energy_move*``**: linearly interpolate ::

      pos_new = pos_low + ((energy_select - energy_low)
                           / (energy_high - energy_low))
                          * (pos_high - pos_low)

5. Keys matching ``energy_pos*`` are **not** interpolated — these
   are static positions (e.g., filter-paddle slot index) that don't
   vary continuously with energy.
6. Apply the resulting interpolated dictionary the same way Path A
   applies the lookup-row dictionary.

The ``energy_move*`` vs ``energy_pos*`` distinction is encoded
at the PV layer in the synApps reference: ``EnergyMoveXPVName``
records the names of motors that are interpolated;
``EnergyPosXPVName`` records the names of motors that are not.

.. note::

   **Pending operator validation.** The interpolation algorithm
   above is the reference algorithm in the **older** synApps
   ``energy`` package; the **newer** deployed
   ``xray-imaging/energy`` package does not implement it (a
   non-calibrated energy raises a ``KeyError`` in
   ``move.py``). When this procedure is formally implemented in
   ``2bm-procedures``, the operator will validate the linear-
   interpolation assumption (motor-by-motor where appropriate;
   some motors may need non-linear curves) and decide whether to
   port the older algorithm or re-implement it directly.

   Operationally, linear interpolation between adjacent calibrated
   Mono points is plausible because the Bragg-angle / vertical-
   offset / slit-walk curves change smoothly with energy. The
   step-pattern visible in the B-slit centre trajectory (see the
   ENERGY-2 ticket / docs) is an artefact of operator save
   batching, not a physical non-linearity that interpolation
   would need to respect.


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

- **Energy outside calibrated range** (Path B) → error before any
  motion is initiated.
- **Calibrated list is empty or corrupt** → error before any motion.
- **Calibrated list contains only one entry** → no interpolation
  possible; only Path A works (exact match required).
- **Front-end shutter fails to close** before the moves → procedure
  must abort before commanding the per-energy moves.
- **One or more motors fail to reach the commanded position**
  within timeout (``AllDoneA`` / ``AllDoneB`` never clears) →
  the energy mode PV is NOT updated and the procedure reports
  partial completion.


Operator walkthrough
--------------------

(TBD as the formal procedure is implemented in ``2bm-procedures``.)


Notes
-----

- The ``energy2bm.json`` ``store_0`` field is the single source of
  truth for calibrated positions across all energy-tracked axes
  (DMM Bragg arms + ``M2 Y``, mirror stripe + table X in Pink mode,
  diagnostic flag Y, B-station slit vertical pair, table3y, filter
  paddle index). Adding a calibrated energy means editing this file,
  not the IOC source or this procedure.
- This procedure is the natural cora ``BeamEnergy`` Method target
  — a single operator-facing "set energy to X keV" surface that
  resolves into the ~15 coordinated motor moves catalogued in
  the :doc:`../manual/item_020` Composite IOCs section.
- The sandbox at ``/home/beams8/DECARLO/conda/sandbox/energy/``
  has three one-off scripts (TomoScan-PV wrapper, enum-PV dispatcher,
  Bragg-arm calibration scan) — useful as snippets when implementing
  the formal procedure, but not part of either deployed energy
  codebase.
