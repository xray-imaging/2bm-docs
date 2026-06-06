===================
Beamline components
===================

Reference inventory of the physical hardware that makes up 2-BM, walked
from the source to the detector. Each component is listed once, with the
fields needed to drive it (Family / role / intrinsic specs) and the
fields needed to reason about how it moves relative to everything else
(what it is mounted on, what rides on top of it).

This page is the source of truth for the beamline as an assembly. Per-
component operating instructions live in :doc:`item_010` (beamline
control) and the :doc:`../ops` section.

.. note::

   "Mounted on" captures the kinematic chain, distinct from compositional
   ownership. A stage mounted on the rotary co-rotates with it; the same
   stage mounted under the rotary translates the rotation axis in lab
   coordinates. Alignment, error propagation, and limit-handling all
   depend on which is which.


Overview
========

.. figure:: ../img/beamline_layout_A342-RT1000.png
   :width: 720px
   :align: center
   :alt: 02-BM XSD beamline layout (APS drawing A342-RT1000, Rev 02)

   02-BM XSD beamline layout (APS drawing **A342-RT1000**, Rev 02,
   05/27/26). Top: building footprint with the 2-BM-A and 2-BM-B
   hutches. Bottom: beamline elevation with components labelled in
   walking order through 2-BM-A; the 2-BM-B hutch (~49–57 m from the
   source) contains a second set of L3 slits at z ≈ 50500 mm.
   Source PDF: `A342-RT1000-02.pdf
   <https://anl.app.box.com/file/2249043253618?s=r8e78v7jj30ggqhmbge0bin2mb4iof7w>`_.

Physical walk, source to detector (z values from the APS reference
table, in millimetres from the centre of the storage-ring straight
section; the beamline runs from z = 24020 at the FE exit mask to
z = 56764 at the photon stop)::

   Storage ring source
     -> A-shutter (front-end)          (in 2-BM-A; z TBD)
     -> L3 Slits + Filters             (z = 25225 mm)
     -> Y3-30 Mirror                   (z = 27626 mm)
     -> Double Multilayer Mono (DMM)   (z = 29335 / 29934 mm)
     -> B-shutter (P6-50 Safety)       (z = 33343 mm)
     -> B-station Slits (L3-style)     (z = 50500 mm; in 2-BM-B)
     -> Sample stack                   (in 2-BM-B; optical table + tower)
     -> Detector system                (MCTOptics + Optique Peter Z + detector table)

Passive components (FE exit mask, K4-21 collimator, Be windows,
white-beam stop, baffles, beam transports, photon stop) sit between
these elements at the z positions listed in the inventory table
below.

Each block below expands into individual components with their intrinsic
properties and kinematic relations.


Beam delivery
=============

The formal inventory of front-end and transport components, with
positions, apertures, materials, and tolerances, is maintained as the
**02-BM Beamline Component Reference Table** (APS_1404611):

https://anl.box.com/s/afme9vpllerzzvsuzqiyxn7aukh7292j

That document is the source of truth for positions and shielding;
the summary below reproduces it in walking order (source to hutch) and
expands the components that are operationally addressed at run time
(slits, mirror, monochromator, safety shutter) into per-component
blocks for cora.

.. note::

   The reference table was last formally updated for the 2013 Sector 02
   Three-Year Safety Review and reflects the pre-APS-U layout. Post-
   APS-U changes (mirror retrofit, source brightness, any added
   components) need a separate reconciliation pass before this page is
   considered final.

Coordinate convention (from the APS_1404611 reference table linked above):

- ``X`` = horizontal, positive outboard [mm]
- ``Y`` = vertical, positive up [mm]
- ``Z`` = along beam from the centre of the straight section [mm]
- ``X`` / ``Y`` positions are relative to the nominal white-beam centreline
- Masks and collimators are referenced to the aperture centre
- ♥ standard bending-magnet centreline (1.35 mrad inboard)
- ♠ alternate centreline (5.1 mrad up at Y3-30 mirror)
- ♦ alternate centreline (5.23 mrad up at Y3-30 mirror)

The Z reference point varies per component class (a superscript on
the Z value in the original APS table; reproduced as the ``ref``
column in the inventory below):

- **1** — upstream face of thermal component (FE exit mask, Be
  windows, white-beam stop, W collimator, safety shutter, SS baffle)
- **2** — centre of optic (slits, mirror, DMM crystals)
- **3** — upstream face of shielding material (K4-21 collimator,
  beam transports, photon stop)


Beam-conditioning inventory (source to hutch)
---------------------------------------------

The table below lists only the components that condition the beam
(shape and energy). Passive hardware — FE exit mask, K4-21 collimator,
Be windows, white-beam stop, W collimator, SS baffle, shielded beam
transports, photon stop — is not reproduced here; the linked APS
reference table at the top of this section is the authoritative
full inventory. Item numbers below match the APS table for cross-
reference.

.. list-table::
   :header-rows: 1
   :widths: 4 32 14 5 45

   * - #
     - Component
     - z [mm]
     - ref
     - Notes
   * - 2
     - L3 Slits with Filters
     - 25225
     - 2
     - Operational; see block below. Splits into L3 Slits (shape) and L3 Filters (energy absorption); shared assembly.
   * - 4
     - Y3-30 Mirror
     - 27626
     - 2
     - Silicon · defines the ♠ / ♦ alternate centrelines · operational, see block below
   * - 5
     - Double Multilayer Monochromator
     - 29335 / 29934
     - 2
     - Silicon · two crystals (Y offsets 9.0 / 41.0 mm) · operational, see block below
   * - —
     - B-station Slits
     - 50500
     - 2
     - Second four-blade L3-style slits in 2-BM-B (no filter changer paired). Not in APS_1404611; z read from layout drawing A342-RT1000-02. Operational, see block below.

Common position tolerances across all rows: dx = dy = 250 µm, dz = 5 mm.

All four items are operational (have command surfaces) and are
expanded in :ref:`operational components <operational-components>`.
The P6-50 safety shutter (item 8c in the APS reference table) is also
operational but gates the beam rather than conditioning it; it appears
as its own block at the end of the operational-components section.


.. _operational-components:

Operational components
----------------------

The L3 slits and filters share an ops page; the mirror and DMM each
have their own. Pre-APS-U pages are noted as such — those entries are
candidates for reconciliation against the post-APS-U layout.

2-BM has two shutters: an A-shutter at the front end (first
gating element on the beamline, in 2-BM-A) and the P6-50 safety
shutter (``B_shutter``) further downstream. Both are listed below.

A-shutter (front-end)
~~~~~~~~~~~~~~~~~~~~~

:Role: First beam-gating element on the beamline; located in 2-BM-A
   upstream of the slits
:Family: Shutter
   (would be a second instance of the Shutter Family already declared
   in the cora 2-BM assets inventory as ``Shutter_2BM``; the
   inventory currently lists only one Shutter — this entry needs to
   be added.)
:Mounted on: Front-end stand (floor-referenced)
:Carries: (beam gating only)
:z position: TBD (upstream of the L3 Slits; not separately listed in
   the APS reference table)
:EPICS prefix: ``S02BM-PSS:FES``
:Open command: ``S02BM-PSS:FES:OpenEPICSC``
:Close command: ``S02BM-PSS:FES:CloseEPICSC``
:Notes:
   Independent of the P6-50 personnel-safety shutter (``B_shutter``,
   below) further downstream. Both must be open for beam to reach
   2-BM-B.

L3 Slits
~~~~~~~~

:Role: Beam-shape conditioning, upstream of the mirror
:Family: Slit
   (listed as "Pending in code" in the cora Equipment BC families
   catalog at ``docs/catalog/families.md``; not yet a registered
   Family. Standard APS L3-20 four-blade slits — two horizontal
   (X−, X+) and two vertical (Y−, Y+) blade motors, plus per-
   direction derived ``Size`` / ``Center`` calc axes.)
:Mounted on: Front-end stand (floor-referenced)
:Carries: (beam conditioning only)
:z position: 25225 mm (ref 2: centre of optic; shared with Filters)
:Position tolerance: 250 µm (x, y), 5 mm (z)
:Reference drawing: L3200000-03.pdf
:As-built drawings: https://anl.box.com/s/sgmoux6db8tsx71pvifzkf2ajopfidqx
:MEDM screen: ``2slit.adl`` (running on ``arcturus``)
:EPICS prefix: ``2bma:`` (horizontal motors ``m14`` for A Slit X−
   [inboard], ``m13`` for A Slit X+ [outboard]; vertical motors
   ``m15`` for A Slit Y+ [up], ``m16`` for A Slit Y− [down])

The slits at 2-BM are standard APS L3-20. Technical as-built drawings
are available `here <https://anl.box.com/s/sgmoux6db8tsx71pvifzkf2ajopfidqx>`_.

.. note::

   "Inboard" follows the APS X convention: positive X is outboard
   (away from the ring centre), negative X is inboard (toward the
   ring centre). The screen below is oriented ``LOOKING UPSTREAM``,
   so on-screen left corresponds to inboard.

.. figure:: ../img/slits_horizontal.png
   :width: 480px
   :align: center
   :alt: 2slit.adl horizontal slits screen

   ``2slit.adl`` — horizontal-slits control screen ("LOOKING
   UPSTREAM", ``Lab`` coordinate system). The two leftmost columns
   drive the individual blade motors: ``2bma:m14`` for the X− blade
   (inboard) and ``2bma:m13`` for the X+ blade (outboard). The
   ``Size`` and ``Center`` columns are calc-driven composites that
   move both blades together to set the aperture width and centre.

.. figure:: ../img/slits_vertical.png
   :width: 480px
   :align: center
   :alt: 2slit.adl vertical slits screen

   ``2slit.adl`` — vertical-slits control screen (same orientation
   and layout as the horizontal screen). The two leftmost columns
   drive the individual blade motors: ``2bma:m15`` for the Y+ blade
   (up) and ``2bma:m16`` for the Y− blade (down). The ``Size`` and
   ``Center`` columns are calc-driven composites that move both
   blades together to set the aperture height and centre.

L3 Filters
~~~~~~~~~~

:Role: Energy filtering (selective absorption upstream of the mirror)
:Family: FilterChanger
   (new Family; not yet declared in the cora equipment BC. Two
   independent paddle sets — upstream and downstream — with up to
   four filter materials per side, plus a None / LowLimit reference
   per side.)
:Mounted on: Front-end stand (shared assembly with L3 Slits)
:Carries: (beam conditioning only)
:z position: 25225 mm (ref 2: centre of optic; shared with Slits)
:Position tolerance: 250 µm (x, y), 5 mm (z)
:Reference drawing: L3200000-03.pdf
:IOC: ``2filter`` (running on ``arcturus``)
:MEDM screens: ``2filter.adl`` (user), ``2filter_setup.adl`` (admin)
:EPICS prefix: ``2bma:`` (filter macro ``fltr1:``; motors ``m17`` upstream,
   ``m18`` downstream; lock calc ``userCalc10``)

**Current configuration.** The L3 filter changer has two independent
paddle sets, mounted upstream and downstream of the slits assembly.
Each side carries up to four filter materials, plus a ``None`` (empty)
position and a ``LowLimit`` hardware reference.

``2filter.adl`` is the operator screen used to drive the filters
during a run; ``2filter_setup.adl`` is the admin screen used to
program the paddle labels and the motor-drive positions each label
maps to. Both screens are part of the ``2filter`` IOC.

.. figure:: ../img/filter.png
   :width: 224px
   :align: center
   :alt: 2filter.adl user screen

   ``2filter.adl`` — operator-facing filter selector. Each button
   moves the corresponding motor to the position bound for that
   material (see admin screen below). ``None`` pulls the paddle
   clear; ``LowLimit`` returns to the hardware reference.

Materials currently bound (read from the screen above):

- **Upstream paddles:** 1 mm C, 150 µm Al, 600 µm Al, 1 mm Al
- **Downstream paddles:** 600 µm Al, 150 µm Al, 300 µm C, 50 µm C

.. warning::

   Only the **downstream** paddle set is currently operational. The
   upstream paddle materials listed above are bound in software but
   the hardware is not in service; selecting them has no effect on the
   beam.

.. figure:: ../img/filter_setup.png
   :width: 480px
   :align: center
   :alt: 2filter_setup.adl admin screen

   ``2filter_setup.adl`` — administrative screen for the L3 filter
   changer. Used to (a) edit the material description on each paddle
   and (b) set the motor-drive position each material maps to, per
   side. The four-paddles-per-side limit and the upstream /
   downstream split are set here. When a material description is
   changed, any open ``2filter.adl`` instance must be closed and
   re-opened to pick up the new label.

**Motor-drive PVs:** upstream ``2bma:m17.VAL``,
downstream ``2bma:m18.VAL``.

**Position bindings (read from ``2filter_setup.adl``):**

.. list-table::
   :header-rows: 1
   :widths: 30 20 30 20

   * - Upstream material
     - ``m17`` position
     - Downstream material
     - ``m18`` position
   * - 1 mm C
     - 2.000
     - 600 µm Al
     - 0.000
   * - 150 µm Al
     - 25.000
     - 150 µm Al
     - 26.000
   * - 600 µm Al
     - 52.000
     - 300 µm C
     - 53.000
   * - 1 mm Al
     - 79.000
     - 50 µm C
     - 80.000
   * - None
     - 106.000
     - None
     - 106.000
   * - LowLimit
     - 0.000
     - LowLimit
     - 0.000

Position units follow the motor record's ``.EGU`` field; the regular
~25-26 spacing across the 0–106 range is consistent with a millimetre
travel.

Y3-30 Mirror
~~~~~~~~~~~~

:Role: Vertical-deflecting mirror; defines the alternate beam centrelines
:Family: Mirror
   (Pending in cora-doga: Asset ``Mirror_2BM`` appears in the
   Pending table at ``docs/deployments/2-bm/assets.md`` and Family
   ``Mirror`` is listed as "Pending in code" at
   ``docs/catalog/families.md``. Composes a mirror body with an
   in-vacuum stripe selector and an external optical-table sub-
   assembly carrying Y / X / Z stages.)
:Mounted on: Optical table (``[Dma:table1]`` via the ``table_full`` IOC)
:Carries: (beam conditioning only)
:z position: 27626 mm (ref 2: centre of optic; mirror-1 axis)
:Position tolerance: 250 µm (x, y), 5 mm (z)
:Material: Silicon
:Mirror length: 0.993 m (used by the angle calc record)
:Reference drawing: 4105091203-300000
:Reference (ops): https://docs2bm.readthedocs.io/en/latest/source/ops/item_045.html#mirror
:MEDM screens: ``2postMirror.adl`` (Y / pitch control), ``table_full.adl`` (optical-table control)
:EPICS prefix: ``2bma:`` (motors listed per sub-system below)

The APS reference-table entry reflects pre-APS-U geometry; the
post-APS-U mirror retrofit (see ``02-BM-MirrorRetrofit_v0.pdf`` in
the beamline records) supersedes that entry and is the basis for the
current ops page linked above. Sets the ♠ (5.1 mrad up) and ♦
(5.23 mrad up) alternate centrelines used by downstream components.

**Mirror Y (pitch) control.** The mirror body sits on two vertical
stages — one near the upstream end, one near the downstream end —
driven independently to set the mirror Y position and the deflection
angle.

.. figure:: ../img/mirror.png
   :width: 480px
   :align: center
   :alt: 2postMirror.adl mirror Y/angle screen

   ``2postMirror.adl`` — mirror Y / angle control screen. The two
   outer columns drive the physical Y motors; the two inner columns
   are derived (calc records).

- **M1 DSY** (downstream Y) — ``2bma:m2``
- **M1 USY** (upstream Y) — ``2bma:m5``
- **Y average** — derived (mean of the two Y motors)
- **Angle [mr]** — derived (deflection angle in milliradians,
  computed from the Y difference and the 0.993 m mirror length)

**In-vacuum stripe selector.** The mirror has four horizontal coating
stripes on its optical surface, selected by translating the mirror
laterally with an in-vacuum X stage:

- **a** — 5 nm Pt (single-layer)
- **b** — W(1.2 nm) / Si(5.37 nm) × 50 multilayer (d-spacing 8.8 % below spec)
- **c** — W(1.2 nm) / Si(3.56 nm) × 50 multilayer (d-spacing 10.1 % below spec)
- **d** — W(1.2 nm) / Si(2.73 nm) × 50 multilayer (d-spacing 9.4 % below spec)

Selector motor: ``2bma:m3``. See the ops page above for the per-
stripe expected-flux curve (``mirror_multilayer_coating.png``).

.. warning::

   ``2bma:m3`` does not have enough travel on its own to reach the
   highest-energy stripe. Reaching that stripe requires a coordinated
   move of ``m3`` together with the optical-table X stages below.
   This coordination is encapsulated by the energy-change IOC; see
   :ref:`composite-iocs`.

**Optical table.** The mirror sub-assembly sits on a multi-motor
optical table that provides additional X (transverse) and Z
(along-beam) translation and rotations. The table is driven through
the ``table_full`` IOC ([Dma:table1]); ``Translate`` / ``Rotate``
columns on the screen are calc-driven composites of the underlying
Motors column.

.. figure:: ../img/mirror_table.png
   :width: 480px
   :align: center
   :alt: table_full.adl optical-table screen

   ``table_full.adl`` ([Dma:table1]) — optical-table control screen.
   Translate / Rotate columns are calc-driven composites; the Motors
   column drives the underlying stages (M0X, M0Y, M1Y, M2X, M2Y, …).

Key table motors:

- **M0X**, **M2X** — table X (two motors driven together; underlying
  EPICS PVs TBD from the IOC configuration). Used by the
  energy-change IOC to extend the in-vacuum ``m3`` travel when the
  highest-energy stripe is requested.
- **M0Y**, **M1Y**, **M2Y** — table Y stages (redundant with the
  ``2postMirror.adl`` per-end Y motors above for fine pitch; the
  per-end motors are the operational surface).
- **Z** — along-beam translation; present but not used operationally.

Double Multilayer Monochromator (DMM)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Energy selection (monochromatic mode)
:Family: Monochromator
   (listed as "Pending in code" in the cora Equipment BC families
   catalog at ``docs/catalog/families.md``; not yet a registered
   Family. Two crystals — upstream (US) and downstream (DS) — each
   with X / Y / Bragg-arm drives, plus global tank Y / Z. Upstream
   crystal carries a split Y (OB / IB) for combined Y translation
   and Z-tilt.)
:Mounted on: Front-end stand (floor-referenced)
:Carries: (beam conditioning only)
:z position: crystal 1 at 29335 mm, crystal 2 at 29934 mm (ref 2: centre of optic)
:Y offset: 9.0 mm (crystal 1), 41.0 mm (crystal 2)
:Position tolerance: 250 µm (x, y), 5 mm (z)
:Material: Silicon
:Inter-crystal spacing: 1323 mm along beam, 765 mm in/out-board (read from screen)
:Reference (ops): https://docs2bm.readthedocs.io/en/latest/source/ops/item_021.html#dmm
:MEDM screen: ``DMMV.adl`` (running on ``arcturus``)
:EPICS prefix: ``2bma:`` (motors listed below)

Insertable: white-beam operation bypasses the DMM. Energy-change
coordination across the DMM motors and the mirror stripe selector
is encapsulated by the energy-change IOC; see :ref:`composite-iocs`.

.. figure:: ../img/dmm.png
   :width: 520px
   :align: center
   :alt: DMMV.adl DMM control screen

   ``DMMV.adl`` — DMM control screen.

**Motors:**

.. list-table::
   :header-rows: 1
   :widths: 22 53 25

   * - Description
     - Role
     - EPICS PV
   * - DMM M2 Z
     - Second crystal translation along the beam relative to the first
     - ``2bma:m8``
   * - DMM USX
     - Global tank upstream X
     - ``2bma:m25``
   * - DMM USY OB
     - Global tank upstream Y outboard side
     - ``2bma:m26``
   * - DMM USY IB
     - Global tank upstream Y inboard side
     - ``2bma:m27``
   * - DMM DSX
     - Global tank downstream X
     - ``2bma:m28``
   * - DMM DSY
     - Second crystal Y relative to the first
     - ``2bma:m29``
   * - DMM US Arm
     - Upstream Bragg-arm rotation
     - ``2bma:m30``
   * - DMM DS Arm
     - Downstream Bragg-arm rotation
     - ``2bma:m31``
   * - DMM M2 Y
     - Second crystal Y (companion to ``M2 Z``)
     - ``2bma:m32``

The tank's upstream end carries two independent Y motors (``USY OB``
and ``USY IB``); their average sets the upstream tank Y position and
their difference produces a Z-tilt around the beam axis. The second
crystal is positioned relative to the first via the ``DSY`` (Y),
``M2 Z`` (along beam), and ``M2 Y`` motors.

P6-50 Safety Shutter (B-shutter)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Personnel-safety shutter for 2-BM hutches
:Family: Shutter
   (already declared in the cora 2-BM assets inventory as
   ``Shutter_2BM``; this entry is the source of its position and
   shielding data.)
:Mounted on: Front-end stand (floor-referenced)
:Carries: (beam gating only)
:z position: 33343 mm (ref 1: upstream face of thermal component)
:Position tolerance: 250 µm (x, y), 5 mm (z)
:Material: W [21 mm]
:Aperture: 60.0 × 44.5 mm
:RSS tag: part of 02-BM-A-P-01 assembly
:EPICS prefix: ``S02BM-PSS:SBS``
:Open command: ``S02BM-PSS:SBS:OpenEPICSC``
:Close command: ``S02BM-PSS:SBS:CloseEPICSC``
:Notes:
   One element of the four-component P6-50 stack (white-beam stop,
   tungsten collimator, safety shutter, SS baffle) installed
   together at z ≈ 330 m. The other three are passive. Both this
   and the upstream A-shutter must be open for beam to reach 2-BM-B.

B-station Slits
~~~~~~~~~~~~~~~

:Role: Beam-shape conditioning at the 2-BM-B entrance, ~21 m
   downstream of the front-end L3 slits
:Family: Slit
   (same standard APS L3-20 four-blade hardware as the front-end
   L3 Slits; reuses the ``Slit`` Family declared there. No filter
   changer is paired with this assembly.)
:Mounted on: Own stand in 2-BM-B (floor-referenced)
:Carries: (beam conditioning only)
:z position: 50500 mm (read from layout drawing A342-RT1000-02; not
   listed in the APS_1404611 reference table)
:Position tolerance: 250 µm (x, y), 5 mm (z) (assumed identical to
   the A-side L3 Slits)
:MEDM screen: ``2slit.adl`` (same screen layout as the A-side slits,
   instantiated with the B-blade motor PVs)
:EPICS prefix: ``2bma:`` (horizontal motors ``2bma:m11`` and
   ``2bma:m12`` for the X pair; vertical motors ``2bma:m9`` for
   Y+ [up] and ``2bma:m10`` for Y− [down])
:Notes:
   These are the slits driven by ``b_slit_top`` (= ``2bma:m9``) and
   ``b_slit_bot`` (= ``2bma:m10``) in the energy-change IOC; the
   vertical pair tracks the per-energy beam position in Mono mode.
   See :ref:`composite-iocs`.

.. note::

   **Horizontal-blade label flip.** The horizontal blade labels on
   the operator side ("B slit Inb" / "B slit outboard") are
   **flipped** with respect to the physical inboard / outboard
   direction: the detector image is mirrored left / right, so the
   on-screen "inboard" actually drives the outboard physical blade
   and vice versa. The mapping of ``2bma:m11`` and ``2bma:m12`` to
   the X+ / X− blades follows the physical convention (positive X
   outboard), not the on-screen labels.

.. figure:: ../img/b_slits_horizontal.png
   :width: 480px
   :align: center
   :alt: 2slit.adl horizontal slits screen (2-BM-B)

   ``2slit.adl`` (2-BM-B instance) — horizontal-slits control screen
   ("LOOKING UPSTREAM", ``Lab`` coordinate system). The two leftmost
   columns (``Slit2H −`` / ``Slit2H +``) drive the individual blade
   motors ``2bma:m11`` and ``2bma:m12``. See the label-flip note
   above. The ``Size`` and ``Center`` columns are calc-driven
   composites that move both blades together to set the aperture
   width and centre.

.. figure:: ../img/b_slits_vertical.png
   :width: 480px
   :align: center
   :alt: 2slit.adl vertical slits screen (2-BM-B)

   ``2slit.adl`` (2-BM-B instance) — vertical-slits control screen
   (same layout as the horizontal screen). The two leftmost columns
   drive the individual blade motors: ``2bma:m9`` for the Y+ blade
   (up, screen-labelled ``Slit2V +``) and ``2bma:m10`` for the Y−
   blade (down, screen-labelled ``Slit2V −``). The ``Size`` and
   ``Center`` columns are calc-driven composites.


Sample stack
============

.. warning::

   **Work in progress / draft.** Everything from this section onward
   (Sample stack, Detector system, Composite IOCs) is a skeleton:
   prose sketches, ASCII trees of the intended kinematic chain, and
   a single concrete Composite-IOC entry (the energy-change IOC). It
   has not been reviewed against the actual hardware and may be
   incomplete or wrong. Treat as a working draft until reconciled.

The sample tower is a kinematic chain of six elements, from the
experimental-hutch floor up to the sample itself. Order matters: stages
below the rotary translate or tilt the rotation axis in lab
coordinates; stages above the rotary ride with the sample and appear in
projection space.

Kinematic chain (bottom to top)::

   Sample optical table          (Y only; floor-referenced)
     +-- Hexapod_2BM              (6-DOF coarse positioner)
          +-- Sample_pitch_lam    (laminography pitch, 0-15 deg)
               +-- Aerotech_ABRS_rotary    (theta axis)
                    +-- Sample_top_X       (co-rotates with theta)
                    +-- Sample_top_Z       (co-rotates with theta)

.. note::

   The cora 2-BM asset inventory at
   ``docs/deployments/2-bm/assets.md`` lists four sample-top
   Devices under ``LinearStage``: ``Sample_top_X``,
   ``Sample_top_Z``, ``Sample_top_Roll``, ``Sample_top_Pitch``.
   This page reproduces ``Sample_top_X`` and ``Sample_top_Z`` as
   the operational surfaces in the kinematic chain; ``Roll`` and
   ``Pitch`` are present in cora but not yet documented per-
   component here.


Sample optical table
--------------------

:Role: Floor-referenced support for the entire sample tower
:Family: OpticalTable
   (new Family; not yet declared in the cora equipment BC)
:Mounted on: Hutch floor
:Carries: Hexapod_2BM (and everything above)
:Degrees of freedom: Y (vertical) only
:Travel: TBD
:Notes:
   Used to set a coarse vertical origin so the hexapod operates near the
   centre of its Y travel. Standard APS optical-table hardware.

Hexapod_2BM
-----------

:Role: Coarse 6-DOF sample positioner
:Family: Hexapod
:Mounted on: Sample optical table
:Carries: Sample_pitch_lam
:Degrees of freedom: X, Y, Z, roll, pitch, yaw
:Travel: TBD per axis
:EPICS prefix: TBD
:Notes:
   Coarse positioning of the entire sample tower. Y is shared with the
   optical table: convention is to set table Y so the sample sits in
   the beam with the hexapod at mid-travel, maximising remaining
   hexapod DOF for fine alignment.

Sample_pitch_lam
----------------

:Role: Laminography pitch axis
:Family: LinearStage
   (mechanically a tilt; would model as a single-axis stage in cora.
   Not yet registered in cora-doga — the closest pending entry is
   "Broader sample-stage motors" under the Pending table at
   ``docs/deployments/2-bm/assets.md``. Consider a dedicated
   ``TiltStage`` Family if more tilt axes appear.)
:Mounted on: Hexapod_2BM
:Carries: Aerotech_ABRS_rotary
:Travel: 0 deg to 15 deg
:EPICS prefix: TBD
:Notes:
   Inserted between hexapod and rotary specifically for laminography.
   At 0 deg the rotation axis is vertical (standard tomography); at
   higher angles the rotation axis is tilted in the beam, enabling
   flat-sample laminography.

Aerotech_ABRS_rotary
--------------------

:Role: Sample rotation axis (theta)
:Family: RotaryStage
:Mounted on: Sample_pitch_lam
:Carries: Sample_top_X, Sample_top_Z
:Travel: -360 deg to +360 deg
:Max speed: 720 deg/s
:Encoder resolution: 0.0001 deg
:Homing offset: 0 deg
:EPICS prefix: TBD
:Notes:
   The four Sample_top_* stages above this axis co-rotate with theta.
   In projection geometry their effect is in the rotating frame, not
   the lab frame.

Sample_top_X
------------

:Role: Fine sample translation, perpendicular to beam (co-rotates with theta)
:Family: LinearStage
:Mounted on: Aerotech_ABRS_rotary
:Carries: (sample)
:Travel: -10 mm to +10 mm
:Max speed: 1 mm/s
:Encoder resolution: 0.0005 mm
:EPICS prefix: TBD

Sample_top_Z
------------

:Role: Fine sample translation, along beam (co-rotates with theta)
:Family: LinearStage
:Mounted on: Aerotech_ABRS_rotary
:Carries: (sample)
:Travel: TBD
:Max speed: TBD
:Encoder resolution: TBD
:EPICS prefix: TBD


Detector system
===============

.. (MCTOptics microscope + Optique Peter Z translation + detector-side
.. optical table. To be drafted; see notes below for the kinematic
.. chain that needs to be captured here.)

The detector microscope (MCTOptics, ~55 m from source) is mounted on
the Optique Peter linear Z stage, which in turn sits on a standard APS
optical table providing roll, pitch, X, and Y. The Z stage moves the
entire microscope along the beam from ~0 (scintillator nearly touching
the sample) out to ~1 m for phase contrast; the optical table is used
to keep the detector centre on the beam as Z moves.

Chain to capture (top of beam down to floor)::

   MCTOptics_objective_{0,1,2}             Family: Objective       (10x / 5x / 1.1x)
   Oryx_5MP_camera                         Family: Camera          (2448 x 2048, 3.45 um pixel)
   Scintillator_LuAG                       Family: Scintillator    (100 um LuAG)
     +-- MCTOptics (Component)             Family: Microscope      <- microscope body; parent of the three above in cora
          +-- MCTOptics_lens_turret        Family: RotaryStage     <- wired sibling under 2-BM (turret selects which objective is in beam)
          +-- Optique_Peter_focus_Z        Family: LinearStage     <- wired sibling under 2-BM (focus Z, 0 to ~1 m along beam)
               +-- Detector optical table                          <- roll / pitch / X / Y
                    +-- Hutch floor

The ASCII chain above shows the kinematic-mounting view (top of beam
down to floor). cora's ownership view is different:
``MCTOptics_lens_turret`` and ``Optique_Peter_focus_Z`` are registered
as Device-level siblings under the ``2-BM`` Unit, then wired into the
``MCTOptics`` Component via ``Plan.wiring`` rather than nested under
it. The objectives, camera, and scintillator are the children of
``MCTOptics`` in cora. See ``docs/deployments/2-bm/assets.md`` for the
canonical composition.


.. _composite-iocs:

Composite IOCs
==============

Some kinematic relations and motion logic are encapsulated inside
custom EPICS IOCs and exposed to the rest of the beamline as
higher-level PVs. Where this is the case, the underlying stack does
not need to be modelled separately in cora: the IOC presents the
composite as a single addressable surface.

Each entry below declares:

:Encapsulates: underlying motors / devices the IOC drives
:Exposes: higher-level PVs presented to EPICS
:Repository: link to source

Convention: components whose kinematic role is fully captured by a
composite IOC do not need a ``Mounted on`` / ``Carries`` chain in the
sections above; they appear only inside the IOC's ``Encapsulates``
list.

Energy-change IOC
-----------------

:Encapsulates: All motors moved together during an energy change.
   The IOC stores per-energy positions in a configuration file and
   drives them as a coordinated move. Categories from the current
   configuration:

   - **Mirror (M1):** angle (``m1angl``) and average Y (``m1avg``,
     derived from ``2bma:m2`` and ``2bma:m5``) — both are deflection-
     geometry parameters held constant in the current configuration
     (``m1angl`` = 2.615 mrad, ``m1avg`` = 0) and would only change
     if the overall beam-deflection geometry were retuned. The
     per-energy mirror action is horizontal stripe selection via
     ``m1_horizontal`` (= ``2bma:m3``) and the optical-table X stages
     ``m1mox`` and ``m1m2x`` (from the ``table_full`` IOC). The
     stripe sets the high-energy cutoff (low-pass filter via the
     coating's reflectivity edge).
   - **DMM:** all nine motors from the table above except ``M2 Z``
     (``2bma:m8``) — that one is not driven by the IOC. Driven set:
     ``dmm_usx``, ``dmm_dsx``, ``dmm_usy_ob``, ``dmm_usy_ib``,
     ``dmm_dsy``, ``dmm_us_arm``, ``dmm_ds_arm``, ``dmm_m2_y``.
   - **Downstream / B-hutch:** ``table3y``, ``flag``,
     ``b_slit_top``, ``b_slit_bot`` — these track the downstream
     beam position. In Mono mode the DMM Bragg geometry shifts the
     beam exit vertically per energy, so the downstream table, flag,
     and B-hutch slits move with it to keep the beam in the pipe and
     centred on the slits. In Pink mode the DMM is bypassed and no
     per-energy tracking is needed.
   - **Filter:** ``fltr1select`` (downstream filter paddle index).

:Modes: Two operating modes are stored:

   - **Mono** — multilayer monochromator engaged. DMM Y motors held
     at 0 (in beam); Bragg arms (``dmm_us_arm`` / ``dmm_ds_arm``) and
     ``dmm_m2_y`` vary per energy to set the Bragg condition and
     compensate the beam-exit offset. Mirror stripe held at the
     lowest position (``m1_horizontal`` = 1.0, table X = 8.0).
     Downstream (``table3y`` / ``flag`` / B-hutch slits) tracks the
     beam-exit shift per energy. Currently configured: 13.374,
     13.574, 18.0, 20.0, 25.0, 25.584 keV.
   - **Pink** — DMM bypassed (all DMM Y motors at −10, out of beam);
     Bragg arms parked at fixed retracted positions. With the DMM
     out the beam goes straight through, so the downstream motors
     are held at neutral positions (``table3y`` = 0, ``flag`` = 0,
     slits wide open). The mirror's stripe selector handles the
     high-energy cutoff: low energies use the soft Pt / low-d
     stripes, higher energies sweep ``m1_horizontal`` plus the table
     X to reach the harder multilayer stripes. Currently configured:
     30, 40, 50, 60 keV.

:Exposes: Higher-level energy-change command surface (PV / RPC TBD;
   consult ``energyApp/`` in the repository).
:Repository: https://github.com/xray-imaging/energy.git
   (standard EPICS app layout — ``energyApp/``, ``iocBoot/``,
   ``configure/``, ``src/``).

**Mirror stripe travel (Pink mode).** ``m1_horizontal`` (the in-vacuum
``2bma:m3``) reaches the low-energy stripes alone; for higher energies
the table X is extended together with it:

.. list-table::
   :header-rows: 1
   :widths: 25 35 40

   * - Energy [keV]
     - ``m1_horizontal`` [mm]
     - ``m1mox`` / ``m1m2x`` [mm]
   * - 30
     - 3.039
     - 8.0 / 8.0
   * - 40
     - 13.0
     - 10.0 / 10.0
   * - 50
     - 39.0
     - 10.0 / 10.0
   * - 60
     - 49.0
     - 29.0 / 29.0

**Stored configurations.** Per-energy positions are persisted with
ISO-8601 timestamps in a ``store_0`` field, so the most recent saved
values for each energy are what the IOC loads on the next move. The
configuration file is the authoritative source for which motors the
IOC drives — adding a new motor to the energy move means adding its
key to that file, not editing the IOC source.

**Why this matters for cora.** The energy-change IOC is the canonical
example of a kinematic / orchestration relation that is not present in
the asset tree but is real and load-bearing for operation. A single
"set energy to 50 keV" command resolves into ~15 coordinated motor
moves spanning the mirror, the DMM, the downstream table, the B-hutch
slits, and the filter changer. From cora's perspective the energy
change should be one Method on a composite "BeamEnergy" surface; the
underlying motor stack stays hidden inside this IOC.

.. (Planned entries — to be drafted as the IOCs are catalogued.)

.. - **MCTOptics IOC** — encapsulates lens-turret routing, focus
..   coupling, camera trigger; exposes ``lens_select`` and friends.
..   Reference: https://github.com/BCDA-APS/tomo-bits

.. - **(Planned) Sample-stack IOC** — could encapsulate the
..   hexapod-Y / optical-table-Y handoff so the rest of the beamline
..   sees a single "sample Y" surface with full range.
