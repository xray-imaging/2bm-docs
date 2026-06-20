============================================
Beamline alignment (white -> pink -> mono)
============================================

.. warning::

   **STATUS: STUB.** The full operator-facing alignment recipe with
   screenshots already lives at :doc:`../ops/item_012`. This page
   is the cora-Procedure-shaped abstraction over that recipe. The
   formal 2bm-procedures script implementation is pending.

   Procedure granularity is an open design decision — the operator
   will decide at implementation time whether this is a single
   ``align_beamline`` Method with three phases (white / pink /
   mono), or three separate Methods (``align_white_beam``,
   ``align_pink_beam``, ``align_mono_beam``) chained in a Plan.
   The stub assumes the single-method-with-three-phases shape for
   now; restructure when the script is written.


Name
----

``align_beamline``


Source
------

Operational walk-through with screenshots: :doc:`../ops/item_012`
(`Beamline alignment`).

Not yet implemented as a standalone script in
`2bm-procedures <https://github.com/decarlof/2bm-procedures>`__.
Future location: `procedures/align_beamline.py
<https://github.com/decarlof/2bm-procedures/blob/main/procedures/align_beamline.py>`__.
The procedure would orchestrate the three sequential alignment
phases (white beam centring, pink-beam tuning, mono-beam DMM
setup) — see Steps below.

Followed at session start (commissioning, start of beamtime, or
after any major optics intervention). Each phase touches a
distinct optic set; phases must run in order because the later
phases assume the earlier ones have already established their
beam state.


Devices
-------

- :doc:`../manual/item_020`: **Mirror M1** — phase 1 (white-beam
  centring) and phase 2 (pink-beam set-angle) use the mirror's
  ``Yaverage`` and angle composite axes; phase 3 (mono) re-asserts
  the mirror state.
- :doc:`../manual/item_020`: **DMM** — phase 3 walks the operator
  through DMM Y stages (``USY OB`` / ``USY IB`` / ``DSY``), the two
  Bragg arms (``2bma:m30`` / ``2bma:m31``), and ``M2 Y`` (``m32``).
  Phase 3 ends with a documented arm-angle / M2 Y target that
  defines the mono setup.
- :doc:`../manual/item_020`: **2-BM-A area detector** at
  ``2bma:m21`` (camera vertical) — operator uses this detector
  through all three phases to image the beam and judge alignment.
- ImageJ + the EPICS_NTNDA plug-in (or equivalent live viewer) —
  external tool dependency for line-profile and centring
  judgements.


Preconditions
-------------

- :doc:`item_003` (``enable_beamline``).
- :doc:`item_007` (``open_b_shutter``) — actually, phase 1 needs
  WHITE beam, so the upstream A-shutter open + DMM out of beam.
  Operator-driven shutter handling per phase rather than a single
  precondition.
- Detector running in 2-BM-A
  (``2bmbOryx5MP medm`` + ``2bmbOryx5MP run`` from the ops
  walk-through).
- No beam-hazardous samples in the path (alignment uses raw
  white / pink / mono beam directly on the detector).


Parameters
----------

- ``do_white`` (boolean, default true) — run phase 1
  (white-beam alignment).
- ``do_pink`` (boolean, default true) — run phase 2
  (pink-beam alignment).
- ``do_mono`` (boolean, default true) — run phase 3
  (mono-beam DMM alignment).
- ``request_steering`` (boolean, default true) — if the white-beam
  vertical profile is not symmetric, the procedure pauses and
  prompts the operator to request beam steering from the APS
  control room (in 10 µrad steps per ops/item_012). If set false,
  procedure proceeds without the steering pause.


Steps
-----

(Distilled from :doc:`../ops/item_012`; the ops page has the full
narrative + screenshots.)

**Phase 1 — White-beam centring**

.. list-table::
   :header-rows: 1
   :widths: 5 35 60

   * - #
     - Action
     - Detail
   * - 1.1
     - Start the 2-BM-A detector and ImageJ live viewer.
     - ``2bmbOryx5MP medm`` + ``2bmbOryx5MP run``; ``ImageJ`` with
       EPICS_NTNDA plug-in configured.
   * - 1.2
     - Lower M1 out of the beam.
     - ``Yaverage = -2 mm``, ``Angle = 0 mrad``.
   * - 1.3
     - Lower the DMM out of the beam.
     - ``USY-OB`` = ``USY-IB`` = ``DSY`` = ``-19 mm``.
   * - 1.4
     - Adjust camera vertical (``2bma:m21``) until the white beam
       is visible.
     - 1 mm Al filter + 20 mm glass; exposure 0.004 s typical.
       Remove the Al filter once the beam is found.
   * - 1.5
     - Verify vertical line profile is symmetric. If not, pause
       and request beam steering from the APS control room in
       10 µrad steps (per ``request_steering`` parameter).
     - Control-room instructions linked from ops/item_012.

**Phase 2 — Pink-beam alignment**

.. list-table::
   :header-rows: 1
   :widths: 5 35 60

   * - #
     - Action
     - Detail
   * - 2.1
     - Insert M1 into the beam.
     - ``Yaverage = 0 mm``, ``Angle = 0 mrad``.
   * - 2.2
     - Recalibrate M1 ``Yaverage`` until the mirror cuts the white-
       beam image in half; recalibrate M1 angle until the reflected
       beam disappears; then reset both to zero.
     - Enable the Proc1 plugin + Save / Enable Flat Field for
       reflected-beam visibility.
   * - 2.3
     - Set M1 angle to ``2.618 mrad`` (= 0.15 deg) to put the
       beamline in pink mode.
     - Move camera up until pink beam is visible.
   * - 2.4
     - Adjust camera vertical until the pink-beam image is
       centred; set camera Y to 0 at that position.
     - Defines the pink-beam centred reference.

**Phase 3 — Mono-beam DMM alignment**

.. list-table::
   :header-rows: 1
   :widths: 5 35 60

   * - #
     - Action
     - Detail
   * - 3.1
     - Set DMM Y stages and Upstream-arm angle to zero.
     - ``USY-OB`` = ``USY-IB`` = ``DSY`` = ``0``;
       Upstream-arm angle = ``0 deg``.
   * - 3.2
     - Recalibrate DMM table height + first-crystal angle: drive
       Y stages until the first crystal cuts the pink beam in
       half; drive Upstream-arm until the reflected beam
       disappears. Then reset to zero.
     - First-crystal calibration.
   * - 3.3
     - Recalibrate second-crystal angle: drive Y stages down by
       10 mm; drive ``DMM M2Y`` down until the second crystal
       cuts the pink beam in half; drive second-crystal angle
       until the reflected beam disappears. Then set
       ``DMM M2Y = 10 mm`` and ``DMM Downstream-arm = 0 deg``.
     - Second-crystal calibration.
   * - 3.4
     - Locate the DMM mono beam.
     - Move DMM into beam (``USY-OB`` = ``USY-IB`` = ``DSY`` = 0);
       set Upstream-arm to ``1.25 deg``; compute target
       ``M2Y = tan(2 * 1.25 deg) * 600 mm = 26.196 mm`` and set
       ``M2Y`` and detector Y (``2bma:m21``) to that value.
   * - 3.5
     - Adjust detector Y until the DMM monochromatic beam is
       visible.
     - Should be near the calculated position.
   * - 3.6
     - Maximise intensity and beam size by adjusting **only**
       Downstream-arm and ``M2Y``.
     - All other DMM axes already calibrated by step 3.2 / 3.3.
   * - 3.7
     - Set the second-crystal angle (Downstream-arm) back to
       ``1.25 deg``.
     - Final mono setup.
   * - 3.8
     - (Optional) Run the
       :doc:`item_014` (``energy_characterization``) procedure to
       verify the absolute energy via the channel-cut crystal
       calibration.
     - Standard follow-on; calibrates the just-aligned mono beam.


Postconditions
--------------

:Satisfies: ``beamline_aligned`` (new condition — not currently a
   precondition of any other procedure, but useful as per-Run
   provenance: each Run can record "aligned on YYYY-MM-DD,
   mono-beam intensity X at energy Y").

:Predicate:
   - Mirror M1 is at the appropriate state for the requested beam
     mode (out for white, in at 2.618 mrad for pink, in for mono).
   - DMM is at the appropriate state for the requested beam mode
     (out for white / pink, in with calibrated Bragg / M2Y for
     mono).
   - Detector vertical position has been adjusted to image the
     selected beam.
   - 2-BM-A detector intensity profile is symmetric.


Failure modes
-------------

- **White-beam vertical profile is asymmetric after steering**:
  multiple 10 µrad steering iterations not converging. May indicate
  upstream optic drift; escalate to APS controls.
- **M1 angle calibration does not produce a clean
  reflected-beam-disappearance signal**: typically indicates the
  Proc1 flat-field reference is stale or the mirror Yaverage is
  off-centre by more than the recalibration step can capture.
  Restart phase 2 from scratch.
- **DMM M2Y measured value disagrees with the calculated
  ``26.196 mm``** by more than a few hundred microns: ops/item_012
  notes that if the optimised value is ``26.046 mm`` the effective
  crystal-to-crystal distance is ``596.56 mm`` (not the
  600 mm assumption). Procedure should record the measured
  crystal-distance value as procedure provenance.
- **Mono beam is dim or has multiple spots after step 3.6**:
  multilayer-stripe contamination or DMM Y misalignment. Restart
  phase 3 from scratch.


Operator walkthrough
--------------------

Full operator-facing recipe with screenshots: :doc:`../ops/item_012`.

The channel-cut energy calibration follow-on (step 3.8) is
:doc:`../ops/item_022` (the focused recipe) or
:doc:`item_014` (the cora-Procedure stub).


Notes
-----

- This procedure is the natural cora "session-start" Method for
  the 2-BM deployment. Every Run that starts at a fresh
  beam-conditioned state would have a recent
  ``align_beamline`` invocation as its precondition or its
  documented provenance.
- The 600 mm inter-crystal distance assumption is worth
  noting: ops/item_012 uses 600 mm in the M2Y target calculation
  and flags 596.56 mm as the observed-effective value. The DMM
  block in :doc:`../manual/item_020` has a different inter-crystal
  spacing value (``1323 mm along beam, 765 mm in / out-board``) which
  appears to describe a different geometric measurement; the
  600 mm figure is the M2Y-relevant one used in the alignment
  formula.
- Procedure granularity (one Method vs three) is a design call
  for the formal 2bm-procedures implementation; the stub assumes
  one Method with three optionally-skippable phases.
