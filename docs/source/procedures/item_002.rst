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


Devices
-------

- :doc:`../manual/item_020`: **Optique_Peter_focus_Z** —
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

The operator is responsible for these before launching:

- Beamline is running in white-beam mode (DMM out, filters set
  for indirect-detection imaging).
- ``MCTOptics`` IOC is reachable (``2bm:MCTOptics:CameraSelected``
  and ``LensSelected`` respond).
- The desired **camera** is selected on the MCTOptics screen
  (Camera 1 = Oryx 5MP, Camera 2 = Oryx 31MP).
- The desired **lens** is selected on the MCTOptics screen
  (Lens1 / Lens2 / Lens3).
- ``Optique_Peter_focus_Z`` is homed and the Z stage is between
  200 and 500 mm (the procedure's safety band; runs that ask for
  values outside this band are rejected at ``__init__``).
- ``2bmb:table3`` is at a known-good baseline pose.
- B-station slits are open to a small square aperture (~1 × 1 mm
  is the design target; the centroid algorithm needs a compact
  bright region above ``threshold_fraction × max``).
- Front-end shutter (FES) is **open**.
- Nobody is in 2-BM-B; PSS interlocks satisfied.
- Sample stage is in a safe out-of-beam position.


Operating envelope (v0.0.1 "build trust" phase)
-----------------------------------------------

- **Z safety band** ``[200, 500]`` mm — enforced at ``__init__``;
  the motor's own ``.HLM`` / ``.LLM`` are not modified.
- **Per-motion confirmation gate** — before every motor motion
  (Z, table AY, table AX) the procedure prints a plan block (PV,
  current value, target value, delta, units) and waits for ``y``
  or ``N`` on stdin. ``N`` aborts cleanly via ``OperatorAbort``;
  the ``try / finally`` then runs the restore path.
- **Snapshot + restore** — at entry the procedure captures the
  full camera state of the active camera (``Acquire``,
  ``AcquireTime``, ``NumImages``, ``ImageMode``, ``TriggerMode``,
  ``TriggerSource``, ``TriggerOverlap``, ``ExposureMode``,
  ``ArrayCallbacks``), the Z stage RBV, **and** the table soft
  axes ``2bmb:table3.AY`` / ``.AX``. On every exit path the camera
  state and Z position are restored. The table AY/AX are restored
  **only on non-success exits** (``OperatorAbort``, exception,
  max-iterations reached without convergence) — on clean
  convergence the optimised AY/AX are left in place as the
  procedure's deliberate output. The restore path prints its plan
  but is **not** gated (it must run even on a panic exit); pass
  ``--confirm-restore`` to gate it.

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
     - Test step in ``table3.AX`` / ``.AY`` used to discover the
       sign and magnitude of the table → centroid Jacobian on
       iteration 0. Default: 50 µrad.
   * - ``exposure_time``
     - number > 0
     - s
     - Per-frame exposure. Default: 0.05.
   * - ``convergence_threshold``
     - number > 0
     - µrad
     - Residual linear slope at or below which the procedure
       stops iterating. Default: 5 µrad.
   * - ``max_iterations``
     - integer ≥ 1
     - —
     - Safety cap. Default: 5.
   * - ``threshold_fraction``
     - 0 < x < 1
     - —
     - Centroid algorithm threshold as fraction of frame max.
       Default: 0.5.
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
       motor. Camera reads (e.g. PixelFormat) still happen.


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
     - **Iteration 0 — calibrate the table → centroid Jacobian.**
       Measure how a small ``ΔAY`` / ``ΔAX`` step moves the
       centroid at fixed Z (decouples the table perturbation from
       the rail tilt the procedure is trying to remove):

       (a) **[gated]** Move Z to ``z_far``; acquire baseline
       centroid (``X_f0``, ``Y_f0``) at AY = AY_baseline.

       (b) **[gated]** Apply ``ΔAY = z_calibration_step`` to
       ``table3.AY``; at the same ``z_far``, acquire centroid
       (``X_f1``, ``Y_f1``). Compute the Jacobian column for AY:
       ``J_AY_X = (X_f1 − X_f0) / ΔAY`` and
       ``J_AY_Y = (Y_f1 − Y_f0) / ΔAY``.
       **[gated]** Restore AY.

       (c) **[gated]** Apply ``ΔAX = z_calibration_step`` to
       ``table3.AX``; at ``z_far``, acquire centroid (``X_f2``,
       ``Y_f2``). Compute ``J_AX_X = (X_f2 − X_f0) / ΔAX`` and
       ``J_AX_Y = (Y_f2 − Y_f0) / ΔAX``. **[gated]** Restore AX.

       (d) Sanity-check: ``|J_AY_X|`` and ``|J_AX_Y|`` should be
       well above noise (else the test step was too small, slits
       were closed, or the table didn't actually move). Abort
       cleanly if either is below ``min_jacobian_um_per_urad``.
     - ``move_motor 2bmbAERO:m1 …``;
       ``move_table_axis 2bmb:table3.AY <baseline + Δ>``
       *(writes the soft PV, then polls the AND of the six jack*
       ``.DMOV`` *PVs);*
       ``acquire_image(cam_prefix, exposure_time)``;
       centre-of-mass over threshold.
   * - 5
     - **Iterative correction.** For ``i = 1 … max_iterations``:

       (a) **[gated]** Acquire frames at ``z_near`` and
       ``z_far``; fit centroids; compute slopes
       ``slope_X = (X_far − X_near) / (z_far − z_near)`` and
       ``slope_Y = (Y_far − Y_near) / (z_far − z_near)`` in
       object-side µm per mm of Z.

       (b) Convert to angular misalignment (µrad).

       (c) If ``|tilt_X|`` and ``|tilt_Y|`` are both below
       ``convergence_threshold``, **break**.

       (d) **[gated]** Apply correction:
       ``AY_new = AY_current − tilt_X × deg/µrad`` (signs from
       calibration); ``AX_new = AX_current − tilt_Y × deg/µrad``.
       Both axes commanded in a single confirmation gate.
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
- ``Optique_Peter_focus_Z`` is back at its pre-procedure RBV
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
  ``resolution_alignment`` (which targets the same
  ``Optique_Peter_focus_Z`` Asset but optimises **lens focus**
  on a fixed sample, not the rail-to-beam angular alignment).
  Both procedures share Assets but operate on different
  physical surfaces.
- Open trigger this procedure creates: register a
  ``Detector_optical_table`` Asset (Family ``OpticalTable``) in
  ``cora/docs/deployments/2-bm/assets.md``, then add a
  ``detector_z_rail_alignment`` entry to that deployment's
  ``procedures.md`` referencing this page.
