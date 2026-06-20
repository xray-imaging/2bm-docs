============================================
Energy characterization (channel-cut crystal)
============================================

.. warning::

   **STATUS: STUB.** The operational walk-through (8-step recipe,
   crystal parameters, Bragg-law formulas, mdaviz fit instructions)
   already lives at :doc:`../ops/item_022`. This page is the
   cora-Procedure-shaped abstraction over that recipe. The formal
   2bm-procedures script implementation is pending.


Name
----

``energy_characterization``


Source
------

The procedure has two operational layers:

- **Broader alignment workflow** that places the beam into mono mode
  and arrives at a DMM ready to be calibrated: :doc:`../ops/item_012`
  (`Beamline alignment` — white beam → pink beam → mono beam).
  The energy-calibration step happens once the DMM mono beam is
  established at the end of that walk-through.
- **Focused calibration recipe** (the 8-step channel-cut rocking-
  curve sub-procedure): :doc:`../ops/item_022`
  (`X-ray energy calibration using channel-cut crystal`).

Not yet implemented as a standalone script in
`2bm-procedures <https://github.com/decarlof/2bm-procedures>`__.
Future location: `procedures/energy_characterization.py
<https://github.com/decarlof/2bm-procedures/blob/main/procedures/energy_characterization.py>`__.
The procedure would scan a removable channel-cut crystal mounted on
the 2-BM-B sample-rotation stage, fit the rocking curve, and report
the measured energy + offset from the nominal DMM setting.


Devices
-------

- **Channel-cut crystal** (calibration Subject — removable reference
  standard, mounted only when calibration is being run):

  - Length: 36 mm
  - Width: 3 mm
  - Lattice spacing: ``2d = 3.84 Å`` (consistent with **Si (220)**;
    operator to confirm crystal cut)
  - Stored off-beamline between calibrations; brought into the beam
    on its own kinematic mount for each calibration run.

- **Rotation stage** for rocking the crystal: the 2-BM-B
  sample-rotation stage (the Aerotech ABRS-150MP described in the
  :doc:`../manual/item_020` sample-stack block, also tracked by cora
  STAGE-3 / cora#164). The crystal is mounted in place of the sample
  for the duration of the calibration.

- :doc:`../manual/item_020`: **DMM** — the optic being calibrated.
  The procedure measures the actual energy at a specific DMM setting
  and computes the offset.

- :doc:`../manual/item_020`: detector + areaDetector ROI + Stat2
  plugins — record reflected intensity in an ROI around the Bragg
  reflection.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- :doc:`item_005` (``set_energy_to_preselect``) — DMM at a nominal
  energy (typically 20 keV per the ops walk-through).
- :doc:`item_007` (``open_b_shutter``) — beam reaches the
  calibration crystal.
- Detector operational with areaDetector ROI plugin enabled.
- Sample-rotation stage clear (no actual sample mounted; the
  channel-cut crystal occupies that slot for the calibration).


Parameters
----------

- ``nominal_energy_keV`` (number > 0) — DMM-side nominal energy at
  which calibration is being performed. Default: 20.0.
- ``rocking_range_deg`` (number > 0) — half-width of the rocking
  scan around the calculated Bragg angle. Default: TBD (operator
  to set per signal-to-noise; ops walk-through suggests "fine
  angular scan" without prescribing a number).
- ``rocking_step_deg`` (number > 0) — angular step of the rocking
  scan. Default: TBD.


Steps
-----

(Distilled from :doc:`../ops/item_022`; the ops page has the full
narrative + screenshots.)

.. list-table::
   :header-rows: 1
   :widths: 5 35 60

   * - #
     - Action
     - Detail
   * - 1
     - Mount the channel-cut crystal on the sample-rotation stage in
       the X-ray beam path.
     - Align so the incident beam fully illuminates the 3 mm crystal
       width and the reflection geometry is symmetric. Zero the
       rotation angle.
   * - 2
     - Set DMM to ``nominal_energy_keV`` via :doc:`item_005`.
     - For 20 keV, expected Bragg angle is ~9.29° (from
       ``arcsin(12.3984 / (2d · E))`` with ``2d = 3.84 Å``).
   * - 3
     - Rotate crystal to the calculated Bragg angle; find the
       reflected beam on the detector.
     - Set an ROI in areaDetector around the reflection; use the
       Stat2 plugin to compute mean intensity in the ROI.
   * - 4
     - Run a fine angular rocking scan around the calculated angle.
     - Record reflected intensity vs angle. Step size and total
       range are operator-set per signal-to-noise.
   * - 5
     - Fit the rocking curve to extract the peak angle
       :math:`\theta_B`.
     - The ops walk-through suggests ``mdaviz`` for interactive
       inspection and fitting:
       ``/APSshare/bin/mdaviz``.
   * - 6
     - Compute the measured energy from Bragg's law.
     - :math:`E_{meas} = 12.3984 / (2d \cdot \sin\theta_B)`.
       Report ``E_meas`` and ``Delta = E_meas - nominal_energy_keV``
       (the latter is for human comparison only — it is NOT stored
       as an offset).
   * - 7
     - Update the calibration table — direct update via
       ``energy add``, no separate offset stored anywhere.
     - Two operator-choice options: (a) tweak optics until the
       rocking-curve peak hits the nominal energy, then
       ``energy add --energy <nominal>`` overwrites the existing
       row with the corrected positions; (b)
       ``energy add --energy <measured>`` adds a new calibrated row
       at the measured value, leaving the existing row in place.
       See :doc:`../ops/item_022` step 7 for the explicit CLI
       invocations. **The cora ENERGY-8 question's "offset folded
       back vs applied separately" framing does not match the
       actual mechanism — there is no offset state.**
   * - 8
     - Verify calibration: repeat the procedure at a second energy
       (e.g. 19 or 21 keV) to check linearity / consistency.
     - Procedure passes if the second-energy ``Delta`` (after the
       step-7 update) is within operator-set tolerance.


Postconditions
--------------

:Satisfies: ``energy_characterized``
   (new condition; not currently a precondition of any other
   procedure but useful as provenance for a Run: each Run can
   record "calibrated at YYYY-MM-DD with offset X keV at nominal Y
   keV").

:Predicate:
   - ``E_measured`` at the nominal DMM setting recorded.
   - If ``|E_measured - nominal_energy_keV|`` exceeds operator
     tolerance, the calibration table has been updated via
     ``energy add`` (either overwriting the existing row at the
     nominal, or adding a new row at the measured value — operator
     choice per step 7). The calibration table itself IS the
     authoritative state; no separate offset is stored.
   - At least two nominal energies sampled and the post-update
     ``Delta`` is within tolerance at both.
   - Channel-cut crystal removed from the beam path at procedure
     end (sample-rotation stage is back to its operational state
     for the next sample).


Failure modes
-------------

- **No reflected beam visible at calculated Bragg angle.** Possible
  causes: crystal mis-aligned, DMM energy mis-set, ROI not on the
  reflection spot. Recovery: realign crystal, re-zero rotation,
  re-check DMM energy with ``caget``.
- **Rocking curve is asymmetric or has multiple peaks.** Possible
  causes: crystal contamination, non-monochromatic input (DMM not
  settled), beam clipped by ROI. Recovery: clean / re-mount crystal,
  re-set DMM (wait for ``AllDoneA`` / ``AllDoneB``), enlarge ROI.
- **``Delta`` is large (> several hundred eV at 20 keV).** Likely
  indicates DMM has drifted significantly since the last
  calibration. The ``energy add`` step still works (the calibration
  table can absorb any single-point correction), but the operator
  should check whether something mechanical needs attention (Bragg
  arm encoder drift, ``M2 Y`` miscount, etc.) before saving.
- **Verification at second energy disagrees after step-7 update.**
  Single-point calibration was applied; multiple nearby energies
  still show large ``Delta``. Likely the underlying issue isn't a
  single-energy shift but a curve-shape problem (e.g., d-spacing
  drift, mechanical zero shift). Operator should re-calibrate the
  affected energies via additional ``energy add`` calls and / or
  investigate the mechanical root cause.


Operator walkthrough
--------------------

- :doc:`../ops/item_012` — broader beamline alignment workflow
  (where this calibration fits as the final step after the mono
  beam is established).
- :doc:`../ops/item_022` — full operator-facing channel-cut
  calibration recipe with screenshots, formula derivations, and the
  Python snippets for the angle ↔ energy conversion.


Notes
-----

- The channel-cut crystal is a **removable reference standard**, not
  installed hardware. Cora can model it as a calibration Subject
  (the same pattern as a resolution phantom — a Subject brought into
  the beam, exercised, then removed).
- The cora-side ``energy_characterization`` Procedure target is this
  page. The crystal is the Procedure's calibration Subject (model
  field: ``si_channel_cut_220`` or similar, with ``two_d_angstrom =
  3.84`` and dimensions 36 x 3 mm).
- The rotation stage used for rocking is the 2-BM-B sample-rotation
  stage (cora STAGE-3 / cora#164 ``SampleRotaryDrive`` / Aerotech
  ABRS-150MP), reused as the rocking axis for the calibration. No
  separate rotation hardware is involved.
- **No "energy offset" state is stored anywhere in the system.**
  The cora ENERGY-8 question's framing — "is the offset folded back
  into the saved table or applied separately?" — implies a stored
  offset that doesn't exist. The actual mechanism: the calibration
  table itself (``energy2bm.json`` ``store_0``) is updated directly
  via ``energy add``, capturing the current motor positions as the
  authoritative calibration for the named energy. Step 7 above is
  the operational form of this. ENERGY-8 should be answered as
  "neither — there is no offset; the table IS the truth, updated in
  place".
