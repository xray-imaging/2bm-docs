==========================================
Detector Z-rail alignment to the beam
==========================================

Walk the Optique Peter detector along its 1 m Z stage with a small
square X-ray aperture defined by the upstream slits, watch the
centroid drift, and use the **detector optical table** beneath the
Z stage to rotate the rail back parallel to the beam.

This procedure removes the **linear** misalignment between the rail
axis and the beam axis. The **non-linear** residual after this
procedure is the intrinsic straightness of the PRO225SL-1000 rail
(~9.5 µm horizontal and vertical straightness per the datasheet) —
that floor is not correctable from the table.

For the hardware reference (table virtual axes, motor map, PV
prefixes), see :doc:`../manual/item_020`.


Name
----

``detector_z_rail_alignment``


Source
------

- **Implementation**: `procedures/detector_z_rail_alignment.py
  <https://github.com/decarlof/2bm-procedures/blob/main/procedures/detector_z_rail_alignment.py>`__
- **Release notes**: `2bm-procedures CHANGELOG
  <https://github.com/decarlof/2bm-procedures/blob/main/CHANGELOG.md>`__


Devices
-------

- :doc:`../manual/item_020`: **PropagationDistance** —
  ``2bmbAERO:m1`` (Aerotech PRO225SL-1000, 1 m travel).
- :doc:`../manual/item_020`: **Detector optical table** —
  synApps ``table.db`` composite ``2bmb:table3``; corrective DoFs
  ``.AX`` (pitch about lab-X, corrects vertical slope) and ``.AY``
  (yaw about lab-Y, corrects horizontal slope). Underlying jacks
  (per item_020.rst) — ``2bmb:m9 / m10 / m11 / m12 / m13 / m14``;
  motion-done detected by ANDing all six ``.DMOV`` fields.

  Not yet a cora Asset — registering one (Family ``OpticalTable``)
  is the open trigger this procedure creates.

- :doc:`../manual/item_020`: **MCTOptics** — read only.
  ``2bm:MCTOptics:CameraSelect`` and ``LensSelect`` (the setpoint
  mbbo records) are read at start to derive the camera
  areaDetector prefix and the lens magnification. The procedure
  does **not** modify either. The lookup is keyed by the mbbo
  **enum index** (returned by a plain ``caget`` without
  ``as_string=True``) so it survives IOC-version differences in
  the display strings (e.g. ``"Camera 1"`` vs
  ``"Camera Selected 1"``).

  ======  ==============  ================  =======================
  Index   CameraSelect    cam_prefix        Camera
  ======  ==============  ================  =======================
  0       ``Camera 1``    ``2bmSP1:``       FLIR Oryx 5MP
  1       ``Camera 2``    ``2bmSP2:``       FLIR Oryx 31MP
  ======  ==============  ================  =======================

  ======  ==============  ================
  Index   LensSelect      Magnification
  ======  ==============  ================
  0       ``Lens1``       1.1×
  1       ``Lens2``       5.0×
  2       ``Lens3``       10.0×
  ======  ==============  ================

  Magnifications are hard-coded in
  ``detector_z_rail_alignment.py``
  (``LENS_MAGNIFICATIONS_BY_INDEX``); update there if the
  installed objectives change.

- :doc:`../manual/item_020`: **Scintillator_LuAG** — passive
  (no command surface).
- :doc:`../manual/item_020`: **B-station slits** —
  ``2bma:m9/m10`` (Y pair) and ``2bma:m11/m12`` (X pair) for
  shaping the small square aperture. Operator-set before the run;
  not modified by the procedure.
- **A-shutter (front-end)** — operator opens before the run; not
  toggled by the procedure.


Preconditions
-------------

In v0.0.1 the operator is responsible for establishing each of the
states below before launching. As the satisfying procedures land,
cora's dependency graph will be able to auto-resolve them.

.. list-table::
   :header-rows: 1
   :widths: 22 48 30

   * - State
     - Predicate (informal)
     - Satisfied by
   * - ``beamline_enabled``
     - ``StaA:SecureM == ON`` AND ``StaB:SecureM == ON`` AND
       ``FES:BeamBlockingM == OFF`` AND
       ``SR-ACIS:2BM:FesPermitM == ON`` (the last aggregates
       BLEPS + APS machine state into one boolean).
     - :doc:`item_003` (``enable_beamline``)
   * - ``a_slits_open``
     - A-station slits open with ``H ≥ 0.5 mm`` and ``V ≥ 0.5 mm``
       (so propagation produces ≈ 1 × 1 mm at the sample / detector
       plane).
     - :doc:`item_004` (``set_a_slits``)
   * - ``energy_configured``
     - Mirror M1 and DMM driven to the energy-dependent positions
       in the ``energy`` package's lookup tables.
     - :doc:`item_005` (``set_energy_to_preselect``)
   * - ``flag_in_beam``
     - ``2bma:m44`` (Flag Y) at the mode-appropriate position.
       Pink: ``0 mm`` user. Mono: per ``energy_move_flag`` lookup
       in `energy2bm.json
       <https://github.com/xray-imaging/energy/blob/main/src/energy/data/energy2bm.json>`__
       (~12-23 mm depending on energy; ``0`` at 30+ keV). See
       item_020's Flag block.
     - :doc:`item_006` (``set_flag_in``)
   * - ``b_shutter_open``
     - ``S02BM-PSS:SBS:BeamBlockingM == OFF`` (inverted enum — OFF
       means NOT blocking, i.e. shutter OPEN).
     - :doc:`item_007` (``open_b_shutter``)
   * - ``b_slits_configured``
     - B-station slits at ``H × V = 1.0 × 1.0 mm`` centred on the
       energy-set vertical Y. Blade motors ``2bma:m9 / m10 / m11
       / m12``.
     - :doc:`item_008` (``set_b_slits``)
   * - ``sample_out_of_beam``
     - The relevant sample-stack axis (mount-dependent) is at its
       out-of-beam position.
     - :doc:`item_009` (``move_sample_out_of_beam``)
   * - ``microscope_configured``
     - MCTOptics lens at 1.1× (slot 0); detector optical table
       ``2bmb:table3.Y`` at the energy-set beam-centre position;
       Z stage ``2bmbAERO:m1`` at a safe mid-band position
       (default 300 mm).
     - :doc:`item_010` (``configure_microscope_for_alignment``)
   * - **FES shutter is open**
     - ``S02BM-PSS:FES:BeamBlockingM == OFF`` (inverted enum).
       Bundled into ``beamline_enabled`` above; called out
       separately because the FES status is a useful sanity check
       directly visible on the synoptic.
     - :doc:`item_003` (``enable_beamline``)
   * - **Z stage in safety band**
     - ``200 ≤ 2bmbAERO:m1.RBV ≤ 500`` (mm). Runs that ask for
       ``z_near`` / ``z_far`` outside this band are rejected at
       ``__init__``.
     - operator (manual move) or
       :doc:`item_010` (``configure_microscope_for_alignment``)
   * - **MCTOptics IOC reachable**
     - ``2bm:MCTOptics:CameraSelect`` and ``LensSelect`` respond
       to ``caget`` (the IOC runs on ``tomdet`` and may not be on
       the same network as some operator hosts).
     - operator (start MCTOptics IOC if not running)
   * - **PSS interlocks satisfied**
     - ``S02BM-PSS:StaA:SecureM == 1`` (ON) AND
       ``S02BM-PSS:StaB:SecureM == 1`` (ON).
       See :doc:`../manual/item_020` (PSS hutch search status block).
     - operator (floor procedure)

The machine-readable form of this table lives in
``procedures/detector_z_rail_alignment.py`` as the module-level
``PRECONDITIONS`` list. It is currently **data only** — the
procedure does NOT runtime-check these. Cora can ingest the list
once the schema lands.


Operating envelope (v0.0.1 "build trust" phase)
-----------------------------------------------

- **Z safety band** ``[200, 500]`` mm — enforced at ``__init__``;
  the motor's own ``.HLM`` / ``.LLM`` are not modified.
- **Per-motion confirmation gate** — before every **table** move
  (calibration perturb / restore, iteration correction) the
  procedure prints a plan block (PV, current value, target value,
  delta, units) and waits for ``y`` or ``N`` on stdin. ``N``
  aborts cleanly via ``OperatorAbort``; the ``try / finally``
  then runs the restore path. **Z measurement moves** stay within
  the safety band and only sample the alignment (don't change
  it), so they are announced but NOT gated by default. Pass
  ``--gate-z`` to gate them too.
- **Snapshot + restore** — at entry the procedure captures the
  full camera state of the active camera (``Acquire``,
  ``AcquireTime``, ``NumImages``, ``ImageMode``, ``TriggerMode``,
  ``TriggerSource``, ``TriggerOverlap``, ``ExposureMode``,
  ``ArrayCallbacks``), the Z stage RBV, **and** the table soft
  axes ``2bmb:table3.AY`` / ``.AX``. On every exit path the camera
  state and Z position are restored. The table AY/AX are restored
  **only on these exits**:

  - ``OperatorAbort`` (operator answered N at a gate).
  - Exception (any RuntimeError, including the divergence guard).
  - max-iterations exhausted **and** the best ``|tilt|`` seen across
    iterations was no better than the starting state.

  On **clean convergence**, the optimised AY/AX stay in place as
  the procedure's deliberate output. On **max-iterations with a
  net improvement** (the common case when the threshold can't be
  reached because of noise), the procedure moves the table back
  to the iteration that gave the best ``|tilt|`` ("best-state commit")
  and leaves it there — the operator still gets the improvement
  that did happen, instead of losing it to baseline restore. The
  log clearly states which path was taken.

  The restore path prints its plan but is **not** gated (it must
  run even on a panic exit); pass ``--confirm-restore`` to gate
  it.

  Caveat: the table restore writes to the ``2bmb:table3.AY/.AX``
  soft PVs; the synApps ``table.db`` kinematic does not always
  perfectly invert a perturb-and-back cycle, so the underlying
  jacks may end up a fraction of a microradian off their
  pre-procedure RBVs (jack hysteresis). True per-jack restore
  would require snapshotting and writing the six jack positions
  directly; not implemented in v0.0.1.
- **Operator-managed surfaces** — MCTOptics camera/lens selection,
  B-station slit apertures, and the FES shutter are NOT touched.
  The procedure reads what the operator has set and adapts.


Parameters
----------

.. list-table::
   :header-rows: 1
   :widths: 22 14 12 52

   * - Name
     - Type
     - Unit
     - Description
   * - ``z_near``
     - number
     - mm
     - Upstream Z anchor for the two-point measurement.
       Default: 200. Must be in ``[200, 500]``.
   * - ``z_far``
     - number
     - mm
     - Downstream Z anchor. Default: 500 (300 mm lever arm).
       Must be in ``[200, 500]`` and ``> z_near``.
   * - ``z_calibration_step``
     - number > 0
     - µrad
     - Test step in ``table3.AY`` and ``.AX`` used to discover the
       2×2 slope-sensitivity matrix M. Default: 50 µrad. Must be
       large enough that the resulting slope change is well above
       the centroid noise floor (~20 µrad for the Oryx 31MP at
       1.1× over a 300 mm Z lever). If calibration aborts with
       "sensitivity matrix near-singular", bump to 100.
   * - ``exposure_time``
     - number > 0
     - s
     - Per-frame exposure. Default: 0.2 (gives a clean bright spot
       on the 1.1× lens at typical 2-BM-B flux). Increase if the
       centroid signal is weak.
   * - ``convergence_threshold``
     - number > 0 (or auto)
     - µrad
     - Residual linear slope at or below which the procedure stops
       iterating. **Default: auto-computed** from the detected lens
       + binning + dz + a fixed rail-straightness floor (~10 µrad
       for the PRO225SL over a 300 mm dz), multiplied by
       ``convergence_safety_margin``. Operator override
       (``--convergence-urad``) wins; a warning is logged if the
       override is below the physical noise floor (procedure
       cannot meaningfully converge to a sub-floor target).
       Reference values at this beamline (dz=300 mm,
       bin=2x2, centroid_noise=1 pix):

       - 1.1x (Lens1): noise floor ~21 urad -> auto threshold ~31 urad
       - 5x   (Lens2): noise floor ~10 urad (straightness floor
         dominates) -> auto threshold ~15 urad
       - 10x  (Lens3): noise floor ~10 urad -> auto threshold ~15 urad
   * - ``convergence_safety_margin``
     - > 1
     - ×
     - Multiplier applied to the noise floor when auto-computing
       the convergence threshold. Default: 1.5. Larger values
       converge sooner (less precision); smaller values closer to
       1.0 push toward the physical floor but may not always
       reach it given residual centroid jitter.
   * - ``centroid_noise_pix``
     - > 0
     - pixels
     - Assumed standard deviation of the centroid fit, used only
       by the auto-threshold calculation. Default: 1.0 (typical
       for COM on a clean spot above threshold). Increase if the
       spot is faint / noisy; decrease if a sub-pixel-stable
       Gaussian fit is in use.
   * - ``max_iterations``
     - integer ≥ 1
     - —
     - Safety cap. Default: 5.
   * - ``damping``
     - 0 < d ≤ 1
     - —
     - Multiplier on the iteration's computed correction. 1.0 =
       full correction, 0.5 (default) = half. Damping < 1 keeps
       us in the linear range across iterations when the
       sensitivity matrix is imperfect or table cross-coupling
       exceeds what a 2×2 linear model captures.
   * - ``divergence_grow_threshold``
     - > 1
     - ×
     - Abort if ``|slope|`` at iteration N exceeds
       ``|slope|`` at iteration N−1 by more than this factor.
       Default: 1.5. Catches runaway positive feedback before
       it walks the spot off the camera.
   * - ``max_correction_per_iter_urad``
     - > 0
     - µrad
     - Hard clip on the per-iteration correction magnitude for
       each table axis. Default: 200. When the calibrated
       sensitivity matrix M is ill-conditioned (table has weak
       authority over one slope direction), M⁻¹ can compute very
       large corrections; the clip keeps each iteration within
       the linear range near the calibration point. Convergence
       happens over more iterations rather than one big move.
   * - ``centroid_algorithm``
     - ``"com"`` | ``"binmask"``
     - —
     - Selects the centroid implementation in
       ``_shared/centroid.py``. Default: ``"com"`` (intensity-
       weighted centre of mass). Use ``"binmask"`` (background-
       thresholded geometric centroid) when the bright pixels of
       the beam lie *outside* the spot envelope (e.g. a saturated
       stripe far from the geometric centre is biasing COM).
       Field-tested 2026-06-14 on a 2-BM-B Oryx 31MP frame
       against an operator hand-eyeballed centre: ``com`` was 8
       px off, ``binmask`` 30-40 px off (the multilayer halo
       extends asymmetrically and pulls the binmask centroid
       off-axis). ``com`` is the right default for this beamline.
   * - ``threshold_fraction``
     - 0 < x < 1
     - —
     - (``com`` only) Threshold as a fraction of the per-frame
       maximum pixel value. Default: 0.5.
   * - ``bg_corner_size``
     - int > 4
     - pixels
     - (``binmask`` only) Per-side length of each of four corner
       boxes used to estimate background statistics. Default: 100.
   * - ``bg_sigma_threshold``
     - > 0
     - σ
     - (``binmask`` only) Threshold = ``bg_median + N × sigma``
       where ``sigma = 1.4826 × MAD`` (Median Absolute Deviation).
       Default: 5.0.
   * - ``frames_per_measurement``
     - int ≥ 1
     - —
     - Acquire and average N frames per centroid measurement.
       Centroid shot-noise drops as ``sqrt(N)`` at N× per-
       measurement acquisition cost. Default: 1 (no averaging).
       Try ``4`` for a ~2× SNR boost on the sensitivity matrix
       without enlarging the calibration step.


   * - ``camera_pixel_um``
     - number > 0
     - µm
     - Camera **sensor** pixel pitch, pre-binning. Default: 3.45
       (Oryx 5MP and 31MP both have 3.45 µm sensor pixels).
       At ``detect_camera_and_lens`` time the procedure reads
       ``cam1:BinX_RBV`` and uses ``sensor_pitch × BinX`` as the
       effective pitch of the delivered image — so 2 × 2 binning
       gives an effective 6.9 µm pitch without any CLI override.
       If ``BinX != BinY`` a warning is logged and BinX is used.
   * - ``--gate-z``
     - flag
     - —
     - Also gate Z measurement moves on y/N. Default off: Z moves
       stay within the safety band and only sample alignment, so
       they're announced but not gated. Table moves are ALWAYS
       gated regardless.
   * - ``--yes``
     - flag
     - —
     - Auto-confirm every motion prompt. Off by default
       (interactive); use for headless / scripted runs only.
   * - ``--confirm-restore``
     - flag
     - —
     - Gate the restore path on ``y/N`` like every other motion.
       Off by default (restore is announced but runs through).
   * - ``--dry-run``
     - flag
     - —
     - Print every planned motion and skip; never moves any
       motor. Camera reads + centroid fits still happen.


.. note::

   **Centroid algorithm.** In v0.0.1 the centroid algorithm
   changed from intensity-weighted COM (``center_of_mass``,
   fraction-of-max threshold) to a background-thresholded
   **geometric centroid** (``centroid_above_background``,
   σ-above-background threshold). Driven by 2-BM-B field testing:
   the DMM beam has strong horizontal multilayer-stripe modulation
   that biases an intensity-weighted COM toward whichever stripe
   happens to be brightest, instead of the geometric centre of the
   illuminated square aperture. The new algorithm gives every
   above-threshold pixel an equal vote, so the centroid tracks the
   geometric centre of the illuminated area regardless of internal
   structure. Median+MAD on corner samples makes the threshold
   robust to bright features spilling into a corner.


Steps
-----

.. list-table::
   :header-rows: 1
   :widths: 5 32 63

   * - #
     - Action
     - PV / call
   * - 1
     - **Detect operator-set configuration.** Read
       ``2bm:MCTOptics:CameraSelected`` and ``LensSelected``;
       derive ``cam_prefix`` and ``magnification`` from the
       module's lookup tables. Abort with a clear message if
       either PV is unreachable or returns an unknown enum value.
     - ``caget 2bm:MCTOptics:CameraSelected``,
       ``caget 2bm:MCTOptics:LensSelected``.
   * - 2
     - **Snapshot pre-procedure state.** Capture the active
       camera's full state and the Z stage RBV. Stored in a
       ``_Snapshot`` dataclass; restored on every exit path.
     - ``caget <cam_prefix>cam1:Acquire``,
       ``...:AcquireTime``, ``...:NumImages``, ``...:ImageMode``,
       ``...:TriggerMode``, ``...:TriggerSource``,
       ``...:TriggerOverlap``, ``...:ExposureMode``,
       ``...:ArrayCallbacks``; ``caget 2bmbAERO:m1.RBV``.
   * - 3
     - **Record table baseline.** Read ``2bmb:table3.AY`` and
       ``.AX`` so the iteration sees the operator's current
       table pose as the zero-correction reference.
     - ``caget 2bmb:table3.AY``, ``caget 2bmb:table3.AX``.
   * - 4
     - **Calibrate the slope-sensitivity matrix M.** For each
       table axis (AY, AX): measure baseline slope, perturb axis
       by ``z_calibration_step``, re-measure slope, restore axis.
       Build the 2×2 matrix M:

       .. code-block:: text

          | Δslope_X |   | M_AY_X  M_AX_X | | ΔAY |
          |          | = |                | |     |
          | Δslope_Y |   | M_AY_Y  M_AX_Y | | ΔAX |

       where slope is in µm/mm and Δaxis in µrad.

       (a) Move Z to ``z_near`` → acquire; move Z to ``z_far`` →
       acquire. Compute baseline slope (``slope0_X``, ``slope0_Y``).

       (b) **[gated]** Perturb ``table3.AY`` by
       ``+z_calibration_step``. Re-measure slope. Compute
       ``M_AY_X = (slope_AY_X − slope0_X) / Δ`` and ``M_AY_Y``
       likewise. **[gated]** Restore AY.

       (c) **[gated]** Perturb ``table3.AX``, re-measure slope,
       compute ``M_AX_X`` and ``M_AX_Y``. **[gated]** Restore AX.

       (d) Sanity-check ``|det(M)|`` ≥ ``min_sensitivity_det``.
       Below this, the procedure aborts with a clear "matrix
       near-singular" message — bump ``z_calibration_step`` to
       100 µrad and retry.

       *This replaces the old centroid-shift-at-z-far "Jacobian"
       formulation, which measured the wrong physical quantity:
       uniform centroid shifts at fixed Z cancel between
       z_near/z_far and leave slope unchanged. The slope
       sensitivity is what actually drives convergence.*
     - ``move_motor 2bmbAERO:m1 …``;
       ``move_table_axis 2bmb:table3.AY <baseline + Δ>``;
       ``acquire_image(cam_prefix, exposure_time)``;
       centre-of-mass over threshold.
   * - 5
     - **Iterative correction.** For ``i = 1 … max_iterations``:

       (a) Acquire frames at ``z_near`` and ``z_far``; fit
       centroids; compute slopes ``slope_X``, ``slope_Y``.

       (b) Convert to angular misalignment ``tilt_X``,
       ``tilt_Y`` (µrad).

       (c) **Divergence guard**: if ````|tilt|```` (Euclidean) at
       this iteration exceeds the previous iteration's by more
       than ``divergence_grow_threshold``, raise ``RuntimeError``
       → restore puts table AY/AX back to baseline. Aborts a
       runaway before it walks the spot off the camera.

       (d) If both ``|tilt_X|`` and ``|tilt_Y|`` are below
       ``convergence_threshold``, **break**.

       (e) **[gated]** Compute corrective ΔAY, ΔAX by solving
       ``M @ (ΔAY, ΔAX) = −(slope_X, slope_Y)`` via
       ``numpy.linalg.inv``; multiply by ``damping``. Apply
       both axes in a single confirmation gate.
     - ``move_table_axis 2bmb:table3.AY <new>``,
       ``move_table_axis 2bmb:table3.AX <new>``.
   * - 6
     - **Restore.** Run by the ``try / finally`` on every exit
       path. Announces the restore plan to stdout (PV →
       captured pre-procedure value), then writes each in turn
       with per-action exception handling so one failure does
       not block the rest. ``table3.AY`` / ``.AX`` are NOT
       restored — the new values are the procedure's output.
     - ``_Snapshot.restore()`` →
       ``caput <cam>cam1:Acquire 0``;
       ``caput …:TriggerMode <captured>``;
       ``caput …:ImageMode <captured>``; (etc.);
       ``move_motor 2bmbAERO:m1 <captured Z>``;
       optionally ``caput …:Acquire 1`` if was running.


Postconditions
--------------

- ``|tilt_X|`` and ``|tilt_Y|`` over the ``[z_near, z_far]``
  lever arm are both below ``convergence_threshold`` (success),
  or the iteration limit was hit and a warning logged.
- ``2bmb:table3.AY`` and ``.AX`` are at the converged values;
  their new positions are logged. (Procedure deliberately does
  not restore these — they're the output.)
- ``PropagationDistance`` is back at its pre-procedure RBV
  (restored from snapshot).
- All snapshotted camera state is back to its pre-procedure
  values: if the camera was running Continuous on entry, it is
  running Continuous on exit; ImageMode / TriggerMode /
  TriggerSource / TriggerOverlap / ExposureMode / NumImages /
  AcquireTime / ArrayCallbacks are all restored.
- FES shutter state is unchanged (procedure does not toggle it).
- The centroid-vs-Z log and iteration history are persisted via
  the cora Procedure record (when ``--no-cora-log`` is not set).


Failure modes
-------------

.. list-table::
   :header-rows: 1
   :widths: 32 68

   * - Symptom
     - Recovery
   * - ``OperatorAbort`` raised at a gate (operator answered
       ``N`` to the y/N prompt).
     - Procedure exits cleanly via ``try / finally``; restore
       path runs and announces what it's putting back. Re-launch
       when ready.
   * - ``ValueError`` at ``__init__`` — ``z_near`` or ``z_far``
       outside ``[200, 500]`` mm, or ``z_near >= z_far``.
     - Adjust ``--z-near`` / ``--z-far`` flags and relaunch.
       Snapshot has not been captured yet at this point; no
       motion has happened; no restore needed.
   * - ``RuntimeError`` reading MCTOptics — unknown camera or
       lens enum, or PV unreachable.
     - Verify MCTOptics IOC is up (host ``tomdet`` for 2-BM);
       set Camera 1 / Camera 2 and Lens1 / Lens2 / Lens3 on the
       MCTOptics screen; relaunch. If the lens is new and not
       in ``LENS_MAGNIFICATIONS``, add an entry.
   * - No signal at ``z_near`` (centroid fit fails — "no signal
       above threshold").
     - Slits closed too tight, beam off-centre, or shutter shut.
       Verify BLEPS status (no Fault latched); open B-station
       slits to a known-good 5 × 5 mm; re-open FES; re-check.
   * - Square aperture exits the camera field of view as Z is
       moved.
     - The initial misalignment is too large for the current
       FOV. Reduce ``z_far`` (lever arm) and / or switch to a
       lower-magnification lens; do a coarse table correction
       by eye first; then restart at the requested lever arm.
   * - Convergence not reached after ``max_iterations``.
     - The restore path returns the snapshot; the iteration
       history is logged. Almost always means the Jacobian sign
       discovery in iteration 0 was wrong (slits drifted,
       centroid algorithm misfit) — re-run with a brighter
       aperture, or a Gaussian fit instead of COM.
   * - ``2bmbAERO:m1`` trips an Aerotech fault during a Z move.
     - The motion call raises ``TimeoutError``; ``try / finally``
       runs restore (which will also fail on the Z axis but will
       restore camera state). Manually clear the drive fault on
       the Aerotech operator screen; verify the table positions
       have not slipped; relaunch.
   * - Table move appears to complete but jacks did not actually
       drive (``move_table_axis`` raises
       ``TimeoutError: jacks did not reach DMOV=1``).
     - The synApps ``table.db`` kinematic engine did not
       propagate the soft-PV change to the underlying motors.
       Verify the MEDM "Use" button isn't required (it shouldn't
       be — the design uses direct writes); inspect the jack
       motor records for fault state.


Operator walkthrough
--------------------

This procedure is intentionally written so an operator can verify
it step-by-step on the MEDM screens:

- **Lens / camera select** — ``mctOptics`` operator screen
  (LensSelect / CameraSelect). Set BEFORE launching the
  procedure; the procedure only reads.
- **Slits** — ``2slit.adl`` for the B-station horizontal +
  vertical screens (see :doc:`../manual/item_020` for the
  label-flip caveat on the horizontal blades). Set BEFORE
  launching.
- **Z stage** — ``2bmbAERO`` motor screen for ``m1``. Watch
  this as each ``[gated]`` Z move executes.
- **Optical table corrections** — ``table_full.adl`` for
  ``2bmb:table3`` (use the Translate column for ``X / Y / Z``
  and the Rotate column for ``AX / AY / AZ``; the composites
  back-drive the underlying corner motors at
  ``2bmb:m9–m14``). Watch the AY / AX text-entry fields update
  as each ``[gated]`` table move executes.
- **Centroid** — the simplest live read is the camera live view
  plus a thresholded ROI in the areaDetector ROI plugin; the
  procedure logs centroid-µm and tilt-µrad to stdout for each
  iteration.

The y/N gate at the terminal IS the operator's safety check —
read the plan block before answering ``y``. If anything looks
wrong (PV name, sign, magnitude), answer ``N`` and the procedure
exits cleanly via the snapshot restore.


Field-test results (v0.0.1, 2026-06-14)
---------------------------------------

First end-to-end convergent run on 2-BM-B:

:Camera: FLIR Oryx 31MP at ``2bmSP2:`` via MCTOptics
:Lens: 1.1× (slot 0)
:Z safety band: 200–500 mm
:Calibration step: 100 µrad
:Damping: 0.5
:Auto-set convergence threshold: 31.4 µrad
:Sensitivity-matrix condition number: **1.1** (essentially
   diagonal — AY ↔ slope_X, AX ↔ slope_Y, no cross-coupling)

Convergence trajectory:

==== ============ ============ ============ ===================
iter ``tilt_X``   ``tilt_Y``   ``|tilt|``   reduction vs prev
==== ============ ============ ============ ===================
1    −169 µrad    +397 µrad    431 µrad     —
2    −85          +203         220          0.51×
3    −46          +97          107          0.49×
4    −20          +49          53           0.50×
5    −10          **+24**      **26**       0.49× ← converged
==== ============ ============ ============ ===================

Each iteration cut ``|tilt|`` almost exactly in half, matching
the ``damping=0.5`` prediction to better than 1%. The final
residual (26 µrad) sits at the PRO225SL rail's intrinsic
straightness floor (~10–20 µrad over a 300 mm sub-range) — the
procedure cannot drive ``|tilt|`` below this regardless of
iteration count. Final table pose:
``AY = −0.0092 deg``, ``AX = −0.0211 deg``.

Run details and the architectural / bug history that got here
are in `2bm-procedures CHANGELOG
<https://github.com/decarlof/2bm-procedures/blob/main/CHANGELOG.md>`__.


Notes
-----

- The PRO225SL-1000 datasheet quotes ±9.5 µm horizontal /
  vertical straightness over the full 1 m travel — that is the
  floor of the non-linear residual once this procedure has
  removed the linear tilt. Operators should not chase residuals
  below that envelope.
- The detector optical table is described in detail in
  :doc:`../manual/item_020` (Detector optical table block; SRI
  geometry, M0X=``m13``, M0Y=``m14``, M1Y=``m12``, M2X=``m10``,
  M2Y=``m9``, M2Z=``m11``; virtual record at ``2bmb:table3``).
- **Sign convention** for ``table3.AX`` / ``.AY`` vs centroid
  drift is discovered at iteration 0 of step 4 — do not hard-
  code a sign in the implementation, derive it from the
  calibration Jacobian.
- **Camera state hygiene** — the procedure changes
  ``TriggerMode``, ``ImageMode``, ``NumImages``, and possibly
  ``AcquireTime`` on the active camera during the run.
  ``acquire_image()`` in ``procedures/_shared/epics.py``
  forces ``TriggerMode=Off`` and ``ImageMode=Single`` before
  every frame so a stale external-trigger configuration (e.g.
  ``TriggerSource=Line2`` for PSO-triggered tomoscan Runs)
  cannot make the call hang. All of these are restored from
  snapshot at exit.
- This procedure is **not** the same as cora's stubbed
  ``resolution_alignment``. The two touch different Assets
  entirely: ``resolution_alignment`` optimises **lens focus**
  via the MCTOptics per-lens focus values (saved by the IOC
  per camera + lens combination), while this procedure walks
  the ``PropagationDistance`` rail stage (``2bmbAERO:m1``) to
  fit and correct rail-to-beam angular alignment. The earlier
  shared-Asset framing in this page reflected the now-corrected
  misconception that ``2bmbAERO:m1`` was a lens-focus motor;
  it is in fact the sample-to-detector Z (propagation) stage.
- Open trigger this procedure creates: register a
  ``DetectorTable`` Asset (Family ``OpticalTable``) in
  ``cora/docs/deployments/2-bm/assets.md``, then add a
  ``detector_z_rail_alignment`` entry to that deployment's
  ``procedures.md`` referencing this page.
