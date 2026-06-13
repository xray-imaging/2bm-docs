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
  virtual record ``2bmb:table3``; corrective DoFs ``.AX`` (pitch
  about lab-X, corrects vertical slope) and ``.AY`` (yaw about
  lab-Y, corrects horizontal slope); centring DoFs ``.X`` and
  ``.Y``.

  Not yet a cora Asset — registering one (Family ``OpticalTable``,
  bound to ``OMS_VME58_2bmb_drive``) is the open trigger this
  procedure creates.

- :doc:`../manual/item_020`: **MCTOptics** Component —
  ``2bm:MCTOptics:LensSelect`` for objective selection.
- :doc:`../manual/item_020`: **Oryx 5MP camera** (camera 0) —
  areaDetector prefix ``2bmSP1:``.
- :doc:`../manual/item_020`: **Scintillator_LuAG** — passive
  (no command surface).
- :doc:`../manual/item_020`: **B-station slits** —
  ``2bma:m9/m10`` (Y pair) and ``2bma:m11/m12`` (X pair) for
  shaping the small square aperture immediately upstream of the
  sample / detector.
- :doc:`../manual/item_020`: **A-shutter (front-end)** —
  ``S02BM-PSS:FES:OpenEPICSC`` / ``CloseEPICSC``.


Preconditions
-------------

- Beamline is running in white-beam mode (DMM out, filters set
  for indirect-detection imaging).
- Optique Peter ``MCTOptics`` IOC is up
  (``2bm:MCTOptics:ServerRunning = Running``).
- ``Optique_Peter_focus_Z`` is homed and at a known position.
- ``2bmb:table3`` motors are at their commissioned baseline
  positions (record before starting so the procedure is
  reversible).
- Nobody is in 19-BM-B; PSS interlocks satisfied.
- Sample stage is in a safe out-of-beam position (the procedure
  does not touch it, but a misaligned rail can put the beam in
  unexpected places).


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
     - Upstream Z anchor for the two-point measurement. Default:
       50 mm above the rail's lower travel limit.
   * - ``z_far``
     - number
     - mm
     - Downstream Z anchor. Default: 350 mm above ``z_near``
       (300 mm lever arm).
   * - ``z_calibration_step``
     - number > 0
     - µrad
     - Test step in ``table3.AX`` / ``.AY`` used to discover the
       sign and magnitude of the table → centroid Jacobian on
       iteration 0. Default: 50 µrad.
   * - ``lens_slot``
     - integer 0–2
     - —
     - Objective slot to use. Default: 0 (1.1× — gives the
       widest field of view).
   * - ``camera_slot``
     - integer 0–1
     - —
     - Camera to use. Default: 0 (Oryx 5MP at ``2bmSP1:``).
   * - ``exposure_time``
     - number > 0
     - s
     - Per-frame exposure. Default: TBD, set by operator from
       a quick free-run check.
   * - ``slit_size_h``
     - number > 0
     - mm
     - B-station horizontal aperture (sets the square width).
       Default: 1.0 mm.
   * - ``slit_size_v``
     - number > 0
     - mm
     - B-station vertical aperture. Default: 1.0 mm.
   * - ``convergence_threshold``
     - number > 0
     - µrad
     - Residual linear slope at or below which the procedure
       stops iterating. Default: 5 µrad (≈ ±1.5 µm shift over a
       300 mm lever arm at 1.1× — below 1 object-side pixel).
   * - ``max_iterations``
     - integer ≥ 1
     - —
     - Safety cap. Default: 5.


Steps
-----

.. list-table::
   :header-rows: 1
   :widths: 5 32 63

   * - #
     - Action
     - PV / call
   * - 1
     - Insert the 1.1× lens.
     - ``caput 2bm:MCTOptics:LensSelect`` *(value =* ``lens_slot`` *,
       default 0; wait for* ``LensSelected`` *to match)*
   * - 2
     - Select camera 0.
     - ``caput 2bm:MCTOptics:CameraSelect`` *(0)*
   * - 3
     - Set exposure time.
     - ``caput 2bmSP1:cam1:AcquireTime`` *(* ``exposure_time`` *)*
   * - 4
     - Set the B-station slits to the requested square.
     - ``caput 2bma:Slit2H:size`` *(* ``slit_size_h`` *)*,
       ``caput 2bma:Slit2V:size`` *(* ``slit_size_v`` *)* — *(verify
       the exact size / centre composite PV names against
       ``2slit.adl``; the underlying blades are*
       ``2bma:m11/m12`` *(X) and* ``2bma:m9/m10`` *(Y))*
   * - 5
     - Open the front-end shutter.
     - ``caput S02BM-PSS:FES:OpenEPICSC 1``; wait for
       ``S02BM-PSS:FES:Position`` to read OPEN.
   * - 6
     - Record baseline. Save current table positions
       ``2bmb:table3.AX``, ``.AY``, ``.X``, ``.Y`` so the procedure
       is reversible on abort.
     - ``caget …`` *(persist into the Run log).*
   * - 7
     - **Iteration 0 — calibrate the table → centroid Jacobian.**

       (a) Move Z to ``z_near``; acquire one image; fit centroid
       (X₀, Y₀).

       (b) Apply test step ``Δ = z_calibration_step`` to
       ``table3.AY``; move Z to ``z_far``; acquire image; fit
       centroid (X₁, Y₁).

       (c) Compute ``J_AY_X = (X₁ − X₀) / Δ`` (object-side µm of
       centroid shift per µrad of AY). Repeat with AX → ``J_AX_Y``.

       (d) Restore table to baseline.
     - ``caput 2bmbAERO:m1 …``; ``caput 2bmSP1:cam1:Acquire 1``;
       ``caget 2bmSP1:image1:ArrayData …`` (or use an external
       client to grab the frame and compute the centroid). For
       the table:
       ``caput 2bmb:table3.AY <baseline_AY + Δ>``.
   * - 8
     - **Iterative correction.** For ``i = 1 … max_iterations``:

       (a) Acquire frames at ``z_near`` and ``z_far``; fit centroids;
       compute slopes
       ``slope_X = (X_far − X_near) / (z_far − z_near)`` and
       ``slope_Y = (Y_far − Y_near) / (z_far − z_near)`` in
       object-side µm per mm of Z.

       (b) Convert to angular misalignment (µrad).

       (c) If ``|slope_X|`` and ``|slope_Y|`` are both below
       ``convergence_threshold``, **break**.

       (d) Apply correction:
       ``AY_new = AY_current − slope_X / J_AY_X`` (signs from
       calibration); ``AX_new = AX_current − slope_Y / J_AX_Y``.
       Wait for both axes to reach the setpoint.
     - ``caput 2bmb:table3.AY <new>``,
       ``caput 2bmb:table3.AX <new>``;
       ``cawait 2bmb:table3.AY.DMOV == 1`` etc.
   * - 9
     - **Verification scan (optional).** Scan
       ``2bmbAERO:m1`` continuously across the full 1 m travel,
       log centroid vs Z. Report (a) the linear residual fit
       (should be ≤ ``convergence_threshold``) and (b) the
       wobble envelope of the non-linear residual.
     - Standard sscan record or an external scan client.
   * - 10
     - Close the front-end shutter.
     - ``caput S02BM-PSS:FES:CloseEPICSC 1``


Postconditions
--------------

- ``|slope_X|`` and ``|slope_Y|`` over the ``[z_near, z_far]``
  lever arm are both below ``convergence_threshold``.
- ``2bmb:table3.AX``, ``.AY``, ``.X``, ``.Y`` are at the
  converged values; their new positions are logged.
- ``Optique_Peter_focus_Z`` is left at ``z_near`` (or at the
  position the operator wants to start a Run from).
- The front-end shutter is closed.
- The centroid-vs-Z log is persisted with the Run record.


Failure modes
-------------

.. list-table::
   :header-rows: 1
   :widths: 32 68

   * - Symptom
     - Recovery
   * - No signal at ``z_near`` after step 5 (image is all noise).
     - Slits closed too tight, beam off-centre, or shutter chain
       interlocked. Verify the BLEPS status (no Fault latched);
       open the B-station slits to a known-good 5 × 5 mm; re-
       check; abort and call beamline staff if still dark.
   * - Square aperture exits the camera field of view as Z is
       moved.
     - The initial misalignment is too large for the 1.1× FOV.
       Reduce ``z_far`` to a shorter lever arm; do a coarser
       table correction by eye on the live view first; then
       restart at the requested lever arm.
   * - Convergence not reached after ``max_iterations``.
     - Restore the table to the baseline values recorded in step
       6; close the shutter; log the residual slopes and the
       last centroid positions; escalate. Almost always means
       the Jacobian sign discovery in iteration 0 was wrong
       (slits drifted, centroid algorithm misfit) — re-run with
       a brighter aperture and a Gaussian fit instead of COM.
   * - ``2bmbAERO:m1`` trips an Aerotech fault during a Z move.
     - Stop the procedure; manually clear the drive fault per
       the Aerotech operator screen; verify the table positions
       have not slipped; resume.


Operator walkthrough
--------------------

This procedure is intentionally written so an operator can step
through it manually on the MEDM screens:

- **Lens / camera select** — ``mctOptics`` operator screen
  (LensSelect / CameraSelect).
- **Slits** — ``2slit.adl`` for the B-station horizontal +
  vertical screens (see :doc:`../manual/item_020` for the
  label-flip caveat on the horizontal blades).
- **Z stage** — ``2bmbAERO`` motor screen for ``m1``.
- **Optical table corrections** — ``table_full.adl`` for
  ``2bmb:table3`` (use the Translate column for ``X / Y / Z``
  and the Rotate column for ``AX / AY / AZ``; the composites
  back-drive the underlying corner motors).
- **Centroid** — the simplest live read is the camera live view
  plus a thresholded ROI in the areaDetector ROI plugin; the
  procedure's slope computation can be done by hand on a
  notepad for the first few iterations.

For a fully automated implementation, see the corresponding cora
Capability / Method / Recipe (when registered) at
``apps/api/src/cora/recipe/``.


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
  drift is discovered at iteration 0 of step 7 — do not hard-
  code a sign in the implementation, derive it from the
  calibration Jacobian.
- This procedure is **not** the same as cora's stubbed
  ``resolution_alignment`` (which targets the same
  ``Optique_Peter_focus_Z`` Asset but optimises **lens focus**
  on a fixed sample, not the rail-to-beam angular alignment).
  Both procedures share Assets but operate on different
  physical surfaces.
- Open trigger this procedure creates: register a
  ``Detector_optical_table`` Asset (Family ``OpticalTable``,
  bound to ``OMS_VME58_2bmb_drive``) in
  ``cora/docs/deployments/2-bm/assets.md``, then add a
  ``detector_z_rail_alignment`` entry to that deployment's
  ``procedures.md`` referencing this page.
