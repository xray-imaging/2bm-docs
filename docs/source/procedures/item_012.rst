================================================
Calibrate the throw of each L3 slit blade motor
================================================

Diagnostic procedure: for each of the four blade motors in a chosen
L3-style slit station, drive the motor by a known mechanical throw
(``± blade_throw_mm`` from baseline), measure how far the
corresponding bright-spot edge moves on the detector, and report
the resulting **pixels-per-mm** slope.

The two H-axis blades should agree on ``|slope|``; the two V-axis
blades should agree on ``|slope|``; H and V means should also agree
to within a few percent when both axes share the same underlying
encoder calibration. A spread between same-axis blades points at
one specific motor record; an H-vs-V mismatch points at the whole
axis pair.

This procedure produces **no deliberate output** -- every blade is
restored to its baseline position after measurement. Intended as a
diagnostic to run when :doc:`item_011` produces an asymmetric image
(e.g. a 1×1 mm aperture rendering as a noticeably non-square
rectangle on the detector), to identify which blade(s) need their
``.MRES`` / encoder direction corrected in the motor IOC.


Name
----

``calibrate_slit_blade_throw``


Source
------

- **Implementation**: `procedures/calibrate_slit_blade_throw.py
  <https://github.com/decarlof/2bm-procedures/blob/main/procedures/calibrate_slit_blade_throw.py>`__
- **Release notes**: `2bm-procedures CHANGELOG
  <https://github.com/decarlof/2bm-procedures/blob/main/CHANGELOG.md>`__


Devices
-------

- :doc:`../manual/item_020`: **A-station L3 Slits** OR **B-station
  L3-style Slits** -- selected via the ``--slit-station`` flag.
  The procedure drives the four underlying blade motor records
  directly (e.g. ``2bma:m9.VAL``), **not** the virtual ``Slit*Hsize``
  /``*Hcenter`` etc. ao records -- the slope it measures is the
  raw motor-mm calibration, independent of the slit calc.
- :doc:`../manual/item_020`: **MCTOptics** -- read only. Camera +
  lens are auto-detected from
  ``2bm:MCTOptics:CameraSelect / LensSelect`` as in
  :doc:`item_002`.
- :doc:`../manual/item_020`: **Detector microscope** (whichever
  Camera/Lens is currently selected) -- used to image the beam
  spot whose bounding-box edges are tracked.


Preconditions
-------------

Same upstream preconditions as :doc:`item_002` (FES + B-shutter
open, ACIS permit, hutch secure, energy configured, sample out
of beam, detector visible). The procedure-specific preconditions:

.. list-table::
   :header-rows: 1
   :widths: 22 48 30

   * - State
     - Predicate
     - Satisfied by
   * - ``slit_aperture_open``
     - The chosen slit has Hsize > 0 and Vsize > 0 large enough
       that the spot survives a ``+ blade_throw_mm`` move on
       every blade. With default ``blade_throw_mm = 0.5``, an
       initial aperture of ≥ 1 mm × 1 mm is recommended.
     - typically the operator runs :doc:`item_011` immediately
       beforehand, then opens to 1 mm before calibrating.
   * - ``spot_visible_at_detector``
     - The current camera + lens combination produces a bright
       spot above the background-MAD threshold. Procedure aborts
       at the initial COM measurement otherwise.
     - cascade of all upstream preconditions for :doc:`item_002`.


Operating envelope (v0.0.1)
---------------------------

- **Per-motion confirmation gate** on every blade move (``+throw``,
  ``-throw``, restore-to-baseline -- 3 gated motions per blade,
  12 total for a 4-blade station).
- **Snapshot + restore** for all four blade baseline positions
  plus camera state. The blade-restore order in the snapshot is
  the order the blade motors are listed in ``SLIT_STATIONS``;
  camera follows.
- **Always restores**, regardless of completion -- this is a
  measurement, not a state-change procedure.
- **Centroid-ROI gating**: bbox edges are detected within a
  ``±edge_roi_half_size_pix`` window around the initial spot
  centroid. The ROI must be large enough to contain the spot at
  ``+ blade_throw_mm`` (where one edge has shifted **outward**
  from baseline). Default 400 pix covers a ~500-px spot with
  ~200 pix shift on each side.
- **Threshold from background MAD**: bbox threshold =
  ``bg_median + N · σ``, with ``σ`` from the median absolute
  deviation of four ROI-corner background patches (corner size
  ``--bg-corner-size``, default 80 pix). Default
  ``N = bg_sigma_threshold = 5.0``.


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
     - Which station's blades to calibrate. Default: ``B``.
   * - ``blade_throw_mm``
     - > 0
     - mm
     - Each blade is driven from ``baseline + this`` to
       ``baseline − this`` (full motor throw is ``2 × this``).
       Default: 0.5 mm.
   * - ``edge_roi_half_size_pix``
     - > 0
     - pix
     - Half-side of the ROI around the initial spot centroid
       used for bbox edge detection. Must contain the entire
       spot at ``+blade_throw_mm`` (one edge has moved further
       outward than baseline). Default: 400.
   * - ``bg_corner_size``
     - > 0
     - pix
     - Per-side length of the four ROI-corner patches used to
       estimate background ``median`` + ``MAD``. Default: 80.
   * - ``bg_sigma_threshold``
     - > 0
     - —
     - Bbox threshold = ``bg_median + N · σ`` with ``σ`` from
       ``1.4826 · MAD``. Default: 5.0.
   * - ``threshold_fraction``
     - 0 < f < 1
     - —
     - Used by the **initial** intensity-weighted COM that sets
       the ROI centre. Same definition as :doc:`item_011`.
       Default: 0.5.
   * - ``frames_per_measurement``
     - ≥ 1
     - —
     - Acquire and average N frames per bbox measurement. With
       multilayer-stripe images, ``N ≥ 4`` notably reduces
       per-edge jitter. Default: 1.
   * - ``exposure_time``
     - > 0
     - s
     - Camera exposure. Default: 0.2.

(Common parameters with :doc:`item_002` and :doc:`item_011` —
``camera_pixel_um``, ``--yes``, ``--confirm-restore``,
``--no-cora-log``, ``--dry-run``, ``--log-level`` — are
documented in their respective pages.)


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
       magnification; read camera binning and frame dimensions.
     - ``caget 2bm:MCTOptics:CameraSelect / LensSelect``;
       ``caget <cam>cam1:BinX_RBV / SizeX_RBV / SizeY_RBV``.
   * - 2
     - **Snapshot pre-procedure state**: each blade motor's
       ``.RBV`` and full camera state.
     - ``caget <blade>.RBV`` for the 4 blades;
       ``caget <cam>cam1:{Acquire, AcquireTime, NumImages,
       ImageMode, TriggerMode, TriggerSource, TriggerOverlap,
       ExposureMode, ArrayCallbacks}``.
   * - 3
     - **Measure baseline spot centroid** (intensity-weighted
       COM at ``threshold_fraction`` of the frame max). Stores
       ``(cx, cy)`` as the ROI centre used by all subsequent
       bbox measurements.
     - ``acquire_image`` × ``frames_per_measurement``; COM via
       ``center_of_mass``.
   * - 4
     - **For each blade motor** (4 iterations):

       (a) **[gated]** Move blade by ``+ blade_throw_mm`` from
       baseline.

       (b) Acquire image, measure bbox within ROI
       (``±edge_roi_half_size_pix`` around baseline centroid):
       background ``median + MAD`` over the four ROI corners;
       threshold = ``bg_median + bg_sigma_threshold · σ``; bbox =
       min/max of the above-threshold mask in y and x.

       (c) **[gated]** Move blade by ``− blade_throw_mm`` from
       baseline (full motor throw = ``2 × blade_throw_mm``).

       (d) Acquire image, measure bbox again.

       (e) **[gated]** Restore blade to baseline.

       (f) Compute edge changes: ``Δedge = bbox_minus − bbox_plus``
       for each of ``top, bottom, left, right``. The edge with
       the largest ``|Δedge|`` is the **primary edge** driven by
       this blade; ``slope = Δedge_primary / (−2 ·
       blade_throw_mm)`` in pixels per mm.
     - ``move_motor <blade> <baseline ± throw>``;
       ``acquire_image``; bbox via NumPy mask.
   * - 5
     - **Report.** Per-blade: primary edge, signed pixel
       displacement, motor throw, signed slope. Aggregate:
       mean ``|slope|`` across H blades, mean across V blades,
       per-axis spread, V/H mean ratio. Warns when:

       - Same-axis spread > 5%.
       - V/H ratio is outside ``[0.9, 1.1]``.
     - log lines only; no PV writes.
   * - 6
     - **Restore.** Run by ``try / finally`` on every exit path.
       All 4 blades restored to snapshot baseline, then camera
       state restored. Blade restore is gated (default
       ``announce_only`` -- single Enter to proceed) unless
       ``--confirm-restore`` is set.
     - ``_Snapshot.restore()`` → 4 ``move_motor`` calls + camera
       caputs via ``safe_restore``.


Postconditions
--------------

On **clean completion**:

- All 4 blade motors are back at their pre-procedure baselines
  (within motor positioning tolerance; verified by
  ``move_motor``'s ``DMOV`` wait).
- Camera state restored to the operator's pre-procedure values.
- Console + cora log contain the per-blade slope table and the
  per-axis aggregate report.
- Slit virtual motors (``Slit*Hsize`` / ``Vcenter`` etc.) are
  **untouched** -- this procedure drives the underlying motor
  records directly.

On **abort**:

- Same: all 4 blades restored, camera restored. The aggregate
  report covers only the blades that completed both ``+throw``
  and ``−throw`` measurements; partial blades are listed with
  ``primary_edge="?"`` and ``slope = 0``.


Failure modes
-------------

.. list-table::
   :header-rows: 1
   :widths: 32 68

   * - Symptom
     - Recovery
   * - ``OperatorAbort`` at any gate.
     - ``try / finally`` runs restore: all blades back to baseline,
       camera back to snapshot. Re-launch when ready.
   * - "could not measure baseline spot centroid" at step 3.
     - Open the slits enough that a spot is visible
       (``Hsize, Vsize ≥ blade_throw_mm + spot margin``). Common
       cause: forgot to reopen after the rezero phase of
       :doc:`item_011`.
   * - "only N pixels above threshold" warning during bbox
       measurement.
     - The spot is too small inside the ROI, or the multilayer
       stripes are pushing the MAD-derived ``σ`` so high that
       even the spot doesn't beat threshold. Try:

       - ``--bg-sigma-threshold 3.0`` (looser threshold), or
       - ``--frames-per-measurement 4`` (lower per-frame jitter
         from stripes), or
       - increase ``--edge-roi-half-size-pix`` if the ROI is too
         small relative to the spot.
   * - V/H ratio noticeably off 1.0 (e.g. 1.55).
     - Not a failure -- this is the diagnostic signal the
       procedure was written to expose. Indicates the
       motor-record calibration (``.MRES``, ``.ERES``, gear
       ratio) of one axis pair is off by the reported ratio.
       Compare to same-axis spread: if both V blades agree but
       both H blades agree too, the IOC mis-calibrates the
       whole axis; if one specific blade is the outlier, that
       single motor record needs attention.
   * - Same-axis spread > 5%.
     - The two blades within one axis disagree on mm-per-pixel.
       The outlier blade is the suspect -- check its
       ``.MRES`` / ``.ERES`` / encoder direction in the motor
       IOC. Cross-reference against the device-asset rows in
       :doc:`../manual/item_020` for that station.


Operator walkthrough
--------------------

- Run :doc:`item_011` first to centre + close + rezero + reopen
  the chosen station's slits, ending with a centred 1 × 1 mm
  aperture. The spot should be roughly in the middle of the
  detector frame.
- Launch with default flags; ``--slit-station B`` is the
  default. To diagnose the A station, pass ``--slit-station A``.
- The first prompt is the ``+throw`` move of the first blade
  (e.g. ``2bma:m9.VAL`` → ``baseline + 0.5 mm``). Confirm
  ``y`` -- the move should be visible on the live view as the
  spot's top or bottom (or left / right) edge shifting.
- Each blade triggers 3 gates: ``+throw``, ``−throw``,
  ``restore``. After all 12, the per-blade slope table and the
  aggregate report appear in the console.
- A V/H ratio close to 1.0 (within ~5%) is the expected outcome
  on a healthy station; anything substantially off is a finding
  worth taking up with the motor IOC config.


Notes
-----

- The procedure does NOT auto-fix the calibration: it only
  identifies which blade(s) are wrong. The fix is a manual edit
  in the motor IOC substitution file followed by an IOC restart.
- "Primary edge" detection is heuristic but robust: a blade
  motion of order 0.5 mm typically shifts one bbox edge by
  ~100-200 pixels while the other three change by < 10 pixels
  (residual jitter from the centroid). The argmax is reliable
  in this regime.
- If two edges shift by comparable amounts, the blade may be
  mis-mounted (driving a diagonal instead of an axis) -- the
  output dict ``all_edge_changes`` is logged so the operator can
  see all four edges and judge.
- Open trigger this procedure creates: extend the cora ``Slit``
  Asset for each station with per-blade ``calibration_slope_pix_per_mm``
  fields populated from this procedure's report (one record per
  blade motor).


Field-test results (v0.0.2, 2026-06-14)
---------------------------------------

First end-to-end runs on both 2-BM stations. Surfaced and fixed
a long-standing V-blade miscal on A; confirmed B is healthy.

:Camera: FLIR Oryx 31MP at ``2bmSP2:`` via MCTOptics
:Lens: 1.1× (slot 0)
:Camera binning: 2 × 2
:Frames per measurement: 4
:Blade throw: ±0.25 mm

A station — before MRES fix
~~~~~~~~~~~~~~~~~~~~~~~~~~~

============= ============ ============ ===================
blade         primary edge slope px/mm  notes
============= ============ ============ ===================
``2bma:m13``  left         339.8        H+ (X+, outboard)
``2bma:m14``  right        328.0        H− (X−, inboard)
``2bma:m15``  bottom       506.8        V+ (Y+, up)
``2bma:m16``  top          486.4        V− (Y−, down)
============= ============ ============ ===================

- H mean = 334 px/mm, V mean = 497 px/mm, **V/H = 1.487** ← WARN.
- Same-axis spread: H 3.5%, V 4.1% (both blades within an axis
  agree; whole-axis V miscal, not single-blade).
- Cross-checked against the operator's pre-existing workaround
  ("setting Vsize = 0.6 mm with Hsize = 1.0 mm gives a square
  image on the detector") and against a hand-bbox of an earlier
  PNG showing the same 1.55× V/H aspect.

Root cause: the motor IOC substitution file
(``/net/s2dserv/xorApps/epics/synApps_5_8/ioc/2bma/iocBoot/ioc2bma/motor.substitutions``)
uses a single template for every motor m1..m44+ — all share
``MRES = 2.5e-4``, ``EGU = "degrees"``, ``VELO = 1``. The per-
axis mechanical differences between the A H blade assembly and
the A V blade assembly were never compensated.

Fix (applied live via ``caput`` with the SET/Use position-
redefinition pattern, persisted via autosave):

.. code-block:: bash

   # For each of m15 and m16:
   caput 2bma:m15.VAL 0       # park at known position
   caput 2bma:m15.SET 1       # enter position-redefine mode
   caput 2bma:m15.MRES 5.95e-4  # = 4e-4 (old) × 1.487 (correction factor)
   caput 2bma:m15.VAL 0       # relabel position in new units (0 → 0)
   caput 2bma:m15.SET 0       # exit Set mode

A station — after MRES fix
~~~~~~~~~~~~~~~~~~~~~~~~~~

============= ============ ============ ===================
blade         primary edge slope px/mm  notes
============= ============ ============ ===================
``2bma:m13``  left         339.7        unchanged ✓
``2bma:m14``  right        328.0        unchanged ✓
``2bma:m15``  bottom       350.4        MRES 4e-4 → 5.95e-4
``2bma:m16``  top          322.3        MRES 4e-4 → 5.95e-4
============= ============ ============ ===================

- H mean = 334 px/mm, V mean = 336 px/mm, **V/H = 1.008** ← PASS.
- Same-axis spread: H 3.5%, V 8.4% (V borderline; likely tighten
  with ``--frames-per-measurement 8``).
- Visually validated: Hsize = Vsize = 1.0 mm now produces a
  square aperture image on the detector.

B station (no fix needed)
~~~~~~~~~~~~~~~~~~~~~~~~~

============= ============ ============ ===================
blade         primary edge slope px/mm  notes
============= ============ ============ ===================
``2bma:m9``   top          170.0        V+ (Y+, up)
``2bma:m10``  bottom       172.0        V− (Y−, down)
``2bma:m11``  left         168.3        H pair
``2bma:m12``  right        167.5        H pair
============= ============ ============ ===================

- H mean = 167.9 px/mm, V mean = 171.0 px/mm,
  **V/H = 1.019** ← PASS.
- Same-axis spread: H 0.5%, V 1.2% (textbook).
- B slopes are ~½ of A's, matching the 2× geometric magnification
  of A's projection at the detector plane (A at z = 25225 mm, B
  and detector both at z ≈ 50500 mm).

Notes from the field test
~~~~~~~~~~~~~~~~~~~~~~~~~

- **DMM image flip.** A's blade-to-edge mapping is mirrored from
  :doc:`../manual/item_020`'s labels (m13 = H+ outboard moves the
  LEFT edge of the spot; m15 = V+ up moves the BOTTOM edge). B's
  mapping is straight (m9 = V+ moves the TOP edge). The DMM
  Bragg reflection between A and B inverts one axis of A's image
  relative to B's at the detector. Worth knowing for any future
  blade-direction debugging.
- **A V-blade tilt.** Even after the MRES fix, the centring-
  sensitivity matrix (from :doc:`item_011`) shows H_sens = 330
  px/mm but V_sens = 134 px/mm — a factor 2.5 asymmetry that the
  blade-throw procedure doesn't see (both axes give ~334 px/mm
  per blade). The discrepancy is consistent with a small tilt
  on the V blade pair that smears the V intensity profile, so
  the COM tracks the slit centre less than 1:1 when Vcenter
  moves. Not a calibration error — a geometric fact about A's V
  kinematic. B's centring sensitivity is much more symmetric
  (H_sens = 170, V_sens = 109, ratio 1.55) consistent with B
  not having the same tilt.

Run details and the architectural / bug history that got here
are in `2bm-procedures CHANGELOG
<https://github.com/decarlof/2bm-procedures/blob/main/CHANGELOG.md>`__.
