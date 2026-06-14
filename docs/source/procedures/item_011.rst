============================================
Centre and close an L3-style slit aperture
============================================

Four-phase L3-style slit procedure: (1) drive the virtual-motor
``Hcenter`` / ``Vcenter`` so the beam image lands at the centre
of the detector frame; (2) incrementally close ``Hsize`` /
``Vsize`` to a target size (typically 0 = fully closed); (3)
**rezero** the virtual motors -- redefine the current physical
pose as the new origin so the four virtual motors read 0 at the
closed + centred position; (4) **reopen** the slits to the
original aperture (snapshot's ``Hsize`` / ``Vsize``, e.g. 1 mm)
so the operator ends with a usable beam through a centred slit.

Phases 3 and 4 are both **default-enabled** but gated per axis;
``--no-rezero`` / ``--no-reopen`` skip the respective phase.

The procedure is parameterised over which slit station to target:
``A`` (front-end L3 Slits at z = 25225 mm) or ``B`` (2-BM-B
entrance L3-style slits at z = 50500 mm, default). The four
virtual-motor PVs for each station follow the same template
``<PREFIX>Hcenter``, ``<PREFIX>Vcenter``, ``<PREFIX>Hsize``,
``<PREFIX>Vsize``; see :doc:`../manual/item_020` for the per-
station prefix.


Name
----

``centre_and_close_slits``


Devices
-------

- :doc:`../manual/item_020`: **A-station L3 Slits** OR
  **B-station L3-style Slits** -- selected via the
  ``--slit-station`` flag. The procedure drives the four ``ao``
  virtual-motor PVs (``Hsize``, ``Hcenter``, ``Vsize``,
  ``Vcenter``) and polls the four underlying blade motors'
  ``.DMOV`` to detect motion completion.
- :doc:`../manual/item_020`: **MCTOptics** -- read only. Camera +
  lens are auto-detected from
  ``2bm:MCTOptics:CameraSelect / LensSelect`` as in
  :doc:`item_002`.
- :doc:`../manual/item_020`: **Detector microscope** (whichever
  Camera/Lens is currently selected) -- used to image the beam
  spot for centroid measurement.


Preconditions
-------------

Same upstream preconditions as :doc:`item_002`
(see its Preconditions table for the full machine-readable list,
including PSS hutches secure, ACIS permit, FES + B-shutter open,
energy configured, sample out of beam). The procedure-specific
preconditions:

.. list-table::
   :header-rows: 1
   :widths: 22 48 30

   * - State
     - Predicate
     - Satisfied by
   * - ``slit_initially_open``
     - The chosen slit has Hsize > 0 and Vsize > 0 (i.e. some
       beam reaches the detector). Procedure aborts at calibration
       if no centroid signal.
     - operator (slit must be open enough to see a beam spot)
   * - ``beam_visible_at_detector``
     - The current camera+lens combination produces a centroid
       above threshold for the current beam state. Tested
       implicitly: calibration's baseline centroid measurement
       fails with the upstream-precondition checklist if no signal.
     - cascade of all upstream preconditions for :doc:`item_002`


Operating envelope (v0.0.1)
---------------------------

- **Per-motion confirmation gate** on every slit move (centring
  perturb / restore / iteration correction, every closing step).
- **Snapshot + restore** for slit Hcenter, Vcenter, Hsize, Vsize
  plus camera state. On clean completion the new (centred,
  closed) state is kept; on abort the procedure restores
  everything to baseline.
- **Centring divergence guard**: aborts if centroid error magnitude
  grows by >1.5× between consecutive iterations.
- **Centring sensitivity sanity floor**: if the 2x2 matrix M has
  ``|det| < min`` (default 1.0 pix²/mm²), the procedure aborts
  with a clear "calibration step too small" message. Operator
  remedy: ``--centring-step-mm 1.0`` or larger.


Parameters
----------

.. list-table::
   :header-rows: 1
   :widths: 22 14 12 52

   * - Name
     - Type
     - Unit
     - Description
   * - ``slit_station``
     - ``"A"`` or ``"B"``
     - —
     - Which station's slits to drive. Default: ``B``.
   * - ``centring_step_mm``
     - > 0
     - mm
     - Perturbation applied to Hcenter / Vcenter during
       sensitivity calibration. Default: 0.5.
   * - ``centring_threshold_pix``
     - > 0
     - pixels
     - Centroid must be within N pixels of the frame centre in
       both axes to declare centring converged. Default: 15
       (at the COM noise floor on 2-BM-B's multilayer-stripe
       images; tightening below ~15 pix requires frame averaging
       via ``--frames-per-measurement``).
   * - ``centring_divergence_grow_threshold``
     - > 1
     - ×
     - Abort centring if ``|err|`` grows by more than this
       factor between iterations. Default: 2.0 (looser than
       :doc:`item_002`'s 1.5× because COM noise on slit-edge
       images can briefly bump ``|err|`` at small corrections
       without indicating a real failure).
   * - ``centring_max_iterations``
     - ≥ 1
     - —
     - Default: 5.
   * - ``centring_damping``
     - 0 < d ≤ 1
     - —
     - Per-iteration correction multiplier. Default: 0.5.
   * - ``centring_max_correction_mm``
     - > 0
     - mm
     - Clip on per-iteration ``|dHcenter|`` / ``|dVcenter|``.
       Default: 1.0.
   * - ``closing_step_mm``
     - > 0
     - mm
     - Size reduction per closing step (H and V alternating).
       Default: 0.1.
   * - ``target_h_size_mm``
     - ≥ 0
     - mm
     - Final H aperture. Default: 0 (fully closed).
   * - ``target_v_size_mm``
     - ≥ 0
     - mm
     - Final V aperture. Default: 0.
   * - ``rezero``
     - bool
     - —
     - If True (default), after closing the procedure rezeros the
       four virtual motors by setting the per-axis ``<axis>set``
       PV to ``Set``, writing 0 to ``<axis>center`` /
       ``<axis>size``, and switching ``<axis>set`` back to
       ``Use``. Each H and V rezero is gated separately and
       irreversible. Set to False (CLI flag ``--no-rezero``) to
       skip and leave the slit virtual motors in their absolute
       coordinate system.
   * - ``reopen``
     - bool
     - —
     - If True (default), after rezero (or after closing, if
       rezero is disabled) the procedure caputs the snapshot's
       original ``Hsize`` / ``Vsize`` back into the slit
       virtual motors. Each axis gated. Operator ends with the
       slits open at the original aperture centred on the
       (possibly new) origin -- ready for use. CLI flag
       ``--no-reopen`` to skip (leaves slits closed).
   * - ``exposure_time``
     - > 0
     - s
     - Camera exposure. Default: 0.2.

(Common parameters with :doc:`item_002` — ``centroid_algorithm``,
``threshold_fraction``, ``bg_corner_size``, ``bg_sigma_threshold``,
``frames_per_measurement``, ``camera_pixel_um``, ``--yes``,
``--confirm-restore``, ``--dry-run``, ``--log-level`` — are
documented there.)


Steps
-----

.. list-table::
   :header-rows: 1
   :widths: 5 32 63

   * - #
     - Action
     - PV / call
   * - 1
     - **Detect operator-set configuration.** Read MCTOptics
       CameraSelect / LensSelect; derive ``cam_prefix`` +
       magnification; read camera binning and frame dimensions
       (``cam1:SizeX_RBV`` / ``SizeY_RBV``); compute frame centre
       in pixel coordinates as the centring target.
     - ``caget 2bm:MCTOptics:CameraSelect / LensSelect``;
       ``caget <cam>cam1:BinX_RBV / SizeX_RBV / SizeY_RBV``
   * - 2
     - **Snapshot pre-procedure state**: slit Hcenter, Vcenter,
       Hsize, Vsize, full camera state.
     - ``caget <slit>Hcenter / Vcenter / Hsize / Vsize``;
       ``caget <cam>cam1:{Acquire, AcquireTime, NumImages,
       ImageMode, TriggerMode, TriggerSource, TriggerOverlap,
       ExposureMode, ArrayCallbacks}``.
   * - 3
     - **Calibrate centring sensitivity matrix M (2×2)**:

       (a) Measure baseline centroid (cx0, cy0).

       (b) **[gated]** Perturb ``Hcenter`` by
       ``+centring_step_mm``; re-measure; restore. Compute
       ``M_Hc_x = (cx_new − cx0) / Δ`` and likewise for
       ``M_Hc_y``.

       (c) **[gated]** Same for ``Vcenter`` to get ``M_Vc_x``,
       ``M_Vc_y``.

       (d) Sanity-check: ``|det(M)|`` ≥
       ``centring_min_sensitivity``. Below floor: abort with
       "increase ``--centring-step-mm``".

       Logs SVD singular values and condition number; warns when
       ``cond > 10``.
     - ``move_table_axis <slit>Hcenter <Δ>``; ``move_table_axis
       <slit>Vcenter <Δ>``; ``acquire_image``; centroid
       (``com`` or ``binmask``).
   * - 4
     - **Iterate centring**: for ``i = 1 … max_iterations``:

       (a) Measure centroid; compute error = centroid − frame_centre.

       (b) If both ``|error_x|`` and ``|error_y|`` below
       ``centring_threshold_pix``, **break (converged)**.

       (c) Divergence guard: abort if ``|error|`` grew by >1.5×
       vs previous iter.

       (d) **[gated]** Compute correction
       ``(dHc, dVc) = M_inv @ (−error)``, multiply by
       ``centring_damping``, clip each axis to
       ``±centring_max_correction_mm``. Apply both Hcenter and
       Vcenter in a single confirmation gate.
     - ``move_table_axis <slit>Hcenter <new>``;
       ``move_table_axis <slit>Vcenter <new>``.
   * - 5
     - **Close slits**: alternating H / V size reduction in
       ``closing_step_mm`` increments. Each step is **[gated]**;
       a centroid measurement after each step verifies the beam
       is still visible. Loop terminates when:

       - Both ``Hsize == target_h_size_mm`` and
         ``Vsize == target_v_size_mm`` (clean completion), OR
       - The centroid algorithm returns ``None`` (beam no longer
         above threshold — expected when slits close past the
         beam envelope). Procedure logs the H / V at which the
         beam disappeared and exits successfully.
     - ``move_table_axis <slit>Hsize <new>``;
       ``move_table_axis <slit>Vsize <new>``;
       ``acquire_image`` + centroid each step.
   * - 6
     - **Phase 3 (default ON): Rezero.** For each axis (H, V),
       separately gated:

       (a) ``caput <slit>{H,V}set "Set"`` -- switches the slit
       calc records into position-redefinition mode.

       (b) ``caput <slit>{H,V}center 0`` and
       ``caput <slit>{H,V}size 0`` -- in Set mode these writes
       redefine the current physical pose as the new origin
       rather than commanding motion.

       (c) ``caput <slit>{H,V}set "Use"`` -- restores motion
       semantics for subsequent moves.

       **IRREVERSIBLE.** Once any rezero write is issued, the
       snapshot-restore for slits is disabled: the pre-rezero
       baseline values are in the old coordinate system, and
       writing them back through the new origin would
       physically move the slits to arbitrary positions.
       Operator can answer ``N`` at the H gate to skip
       rezeroing entirely (keeps the centred + closed state);
       answering ``N`` at the V gate after the H rezero
       succeeded leaves H rezeroed and V untouched.

       Skip the entire phase via the ``--no-rezero`` flag.
     - 4 ``caput`` per axis: ``<slit>Hset``, ``<slit>Hcenter``,
       ``<slit>Hsize``, ``<slit>Hset`` for H; same for V.
   * - 7
     - **Phase 4 (default ON): Reopen.** Per axis, gated:
       caput the snapshot's original ``Hsize`` (resp. ``Vsize``)
       back into the virtual motor. If phase 3 (rezero) ran,
       this is interpreted in the new coordinate system -- the
       slits open by the original aperture (e.g. 1 mm) centred
       on the new origin (= beam axis). If rezero was skipped,
       the aperture size write still works as an absolute size;
       only the coordinate-system label is unchanged.

       Skip with the ``--no-reopen`` flag (operator can leave
       the slits closed for inspection).
     - ``move_table_axis <slit>Hsize <snapshot.Hsize>``;
       ``move_table_axis <slit>Vsize <snapshot.Vsize>``.
   * - 8
     - **Restore.** Run by ``try / finally`` on every exit path.
       Slits are restored to baseline **unless** either:

       - Procedure completed centring + closing cleanly (slits
         are the deliberate output), OR
       - Any rezero write was issued (snapshot is meaningless
         in the new coordinate system).

       Camera state always restored. Slit restore order:
       Hcenter → Vcenter → Hsize → Vsize.
     - ``_Snapshot.restore(restore_slits=…)`` → four
       ``move_table_axis`` calls then the camera-state caputs.


Postconditions
--------------

On **clean completion**:

- Centroid is within ``centring_threshold_pix`` of the frame
  centre in both axes (at the time of the final centring
  measurement).
- ``Hsize == target_h_size_mm`` and ``Vsize == target_v_size_mm``
  (typically both 0) **or** the beam was no longer above
  threshold at a size slightly above target.
- Camera state restored to the operator's pre-procedure values.
- The slit virtual motors' final state depends on which phases
  ran:

  - **Default (rezero + reopen both on)**: virtual motors read
    ``Hcenter = Vcenter = 0`` and ``Hsize = Vsize = original
    aperture`` (e.g. 1 mm). New origin is defined; slits are
    open at the original aperture centred on the beam axis.
  - **``--no-reopen``**: virtual motors read ``Hsize = Vsize = 0``
    (slits closed). Origin set if rezero ran.
  - **``--no-rezero`` (+ default reopen)**: slits open at the
    original aperture, centred on the corrected pose;
    coordinate system unchanged from pre-procedure.
  - **``--no-rezero --no-reopen``**: slits closed at the new
    centred pose, no coordinate change. Useful for verification.

On **abort**:

- All four slit virtual motors restored to snapshot baseline.
- Camera state restored.


Failure modes
-------------

.. list-table::
   :header-rows: 1
   :widths: 32 68

   * - Symptom
     - Recovery
   * - ``OperatorAbort`` at any gate.
     - ``try / finally`` runs restore: slits back to baseline,
       camera back to snapshot. Re-launch when ready.
   * - Calibration baseline centroid fails ("no signal above
       threshold").
     - One of the upstream preconditions failed -- the error
       message prints a checklist. Open FES + B-shutter, verify
       beam, slits open wide enough, etc.
   * - Sensitivity matrix near-singular.
     - The slit-centre perturbation produced negligible
       centroid motion. Try larger ``--centring-step-mm``
       (start with 1.0 mm; up to several mm for very-distant
       slit / detector geometries).
   * - Centring diverges (error grows >1.5× iter-to-iter).
     - The sensitivity matrix M has a sign or magnitude error.
       Re-run calibration with a larger ``--centring-step-mm``.
   * - Centring runs out of iterations without converging.
     - Restore puts slits back to baseline. Increase
       ``--centring-max-iterations`` or relax
       ``--centring-threshold-pix``.
   * - Closing step makes the beam disappear earlier than
       expected (e.g. at H = 0.3 mm rather than 0.1).
     - Not a failure -- the procedure logs the H/V at which the
       beam disappeared and exits successfully. The "closed
       past beam" criterion is intentional: it confirms the
       slits actually cut off the beam.


Operator walkthrough
--------------------

- Confirm upstream preconditions (FES, B-shutter, beam, sample
  out of beam, detector visible on MEDM).
- Launch with default flags; ``--slit-station B`` is the
  default.
- The first ~4 prompts confirm centring-sensitivity calibration
  motions (perturb Hc, restore, perturb Vc, restore). Each is a
  small move (default 0.5 mm) and should produce a visible
  centroid shift on the live view.
- After M is logged with a reasonable condition number (< 10),
  iteration begins. Each iter prompts once for the (Hcenter,
  Vcenter) pair correction. Centroid should drop toward the
  frame centre.
- Once centred (within ``centring_threshold_pix``), the closing
  phase starts. Each step is a 0.1 mm reduction; the operator
  sees the spot shrink on the live view.
- Procedure exits when both sizes reach 0 or the beam
  disappears.

The y/N gate at the terminal is the operator's safety check --
read the plan block before answering ``y``. If anything looks
wrong (PV name, sign, magnitude), answer ``N`` and the
procedure restores cleanly.


Notes
-----

- The 8.6 µrad-scale precision of :doc:`item_002` is not the
  goal here: this procedure positions the slits in millimetres,
  not the rail in microradians. ``centring_threshold_pix = 5``
  with bin 2×2 at 1.1× means the centroid is within 5 × 6.27 /
  1.1 ≈ 28 µm object-side -- fine for slit centring.
- The closing phase is intentionally **sequential** (H step,
  measure, V step, measure, ...) rather than simultaneous: each
  step gives the operator a fresh image to confirm the beam is
  shrinking as expected, and the centroid measurement detects
  the "beam disappeared" condition that ends the loop.
- The slit virtual motors (``ao`` records) drive downstream calc
  records which in turn drive the underlying blade motors. The
  procedure uses ``move_table_axis`` for motion-done because the
  pattern is the same as the detector optical table: write the
  soft PV, poll the underlying motors' ``.DMOV``.
- Open trigger this procedure creates: register a per-station
  ``Slit`` Asset on the cora side and a ``centre_and_close_slits``
  Procedure record in
  ``cora/docs/deployments/2-bm/procedures.md``.
