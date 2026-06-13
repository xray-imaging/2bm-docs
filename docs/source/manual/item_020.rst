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
control) and the :doc:`../ops` section. Structured, cora-consumable
recipes that *use* this hardware live in :doc:`../procedures`.

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
blocks for `cora <https://github.com/xmap/cora>`__.

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

.. note::

   **2-BM-A motor controller.** Every ``2bma:mNN`` motor cited in
   the 2-BM-A blocks below (L3 Slits, Y3-30 Mirror, DMM, B-station
   Slits, etc.) is a slot on the OMS-VME58 card in the ``ioc2bma``
   crate — cora Asset ``OMS_VME58_2bma_drive``. None of those
   driven stages are themselves modelled as cora Assets yet (the
   front-end / beam-conditioning band stays in cora's Pending
   list); the controller Asset ships in isolation as the
   addressability handle for "controller-level" Procedures.

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
   (Pending in cora: Asset ``Y3-30_mirror`` appears in the
   Pending table at ``docs/deployments/2-bm/assets.md`` (renamed
   from ``Mirror_2BM`` per cora's #89) and Family ``Mirror`` is
   listed as "Pending in code" at ``docs/catalog/families.md``.
   Composes a mirror body with an in-vacuum stripe selector and
   an external optical-table sub-assembly carrying Y / X / Z
   stages.)
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

The sample tower is a kinematic chain of six elements, from the
experimental-hutch floor up to the sample itself. Order matters: stages
below the rotary translate or tilt the rotation axis in lab
coordinates; stages above the rotary ride with the sample and appear in
projection space.

Kinematic chain (bottom to top)::

   Sample optical table              (Y only; floor-referenced)
     +-- Hexapod_2BM                  (6-DOF coarse positioner)
          +-- Sample_pitch_lam        (laminography pitch, 0-20 deg)
               +-- fixed -10 deg wedge (cancels +10 deg stage hold)
                    +-- Aerotech_ABRS_rotary  (theta axis)
                         +-- Sample_top_X     (co-rotates with theta)
                         +-- Sample_top_Z     (co-rotates with theta)

.. note::

   The cora 2-BM asset inventory at
   ``docs/deployments/2-bm/assets.md`` lists four sample-top
   Devices: ``Sample_top_X``, ``Sample_top_Z``,
   ``Sample_top_Roll``, ``Sample_top_Pitch``.
   ``Sample_top_X`` and ``Sample_top_Z`` are the Kohzu CYAT-070
   stages above the rotary and are documented below.
   ``Sample_top_Roll`` and ``Sample_top_Pitch`` correspond to the
   hexapod's Roll (``2bmHXP:m5``) and Pitch (``2bmHXP:m4``) axes —
   they are part of the **Hexapod_2BM** block below, not separate
   per-component stages above the rotary. cora classifies these
   two as ``PseudoAxis`` Assets ("virtual DoFs over the 2bmHXP
   hexapod-kinematics solver"), confirming the same mapping.

.. note::

   **For cora PV mapping.** Every ``2bmb:mNN`` PV cited in the
   Sample stack and Detector system sections has been verified
   against the ioc2bmb IOC: OMS-VME58 motors ``m1``–``m91`` are
   declared in ``motor.substitutions``, and the Aerotech Ensemble
   axes ``m100``/``m101``/``m102`` in ``AsynMotor.substitutions``
   (asyn ports ``AeroE1``/``AeroE2``/``AeroE3``). The motor records
   themselves carry generic ``DESC`` strings (``"motor $(N)"``), so
   the per-Device role (e.g. ``Sample_top_X = 2bmb:m18``) is not
   recoverable from the IOC alone — it is configured in mctoptics
   substitutions, tomoScanStream, ``table.db`` calls, and this
   page. When registering cora Devices against ioc2bmb PVs, treat
   this page as the source of truth.


Sample optical table
--------------------

:Role: Floor-referenced support for the entire sample tower
:Family: OpticalTable
   (new Family; not yet declared in the cora equipment BC)
:Mounted on: Hutch floor
:Carries: Hexapod_2BM (and everything above)
:Degrees of freedom: 4 motors (Y, downstream X, upstream X, Z). In
   routine operation only Y is moved; the X and Z motors exist but
   are not used for day-to-day sample positioning.
:EPICS:

   ============  ==============  ====================================
   Axis          PV              MEDM label
   ============  ==============  ====================================
   Y (vertical)  ``2bmb:m24``    ``Sample table Y``
   Z             ``2bmb:m20``    ``Sample table Z``
   USX           ``2bmb:m21``    ``Sample table USX`` (upstream X)
   DSX           ``2bmb:m22``    ``Sample table DSX`` (downstream X)
   ============  ==============  ====================================

   No combined ``table.db`` virtual record is loaded for this table —
   the four motors are addressed directly.
:Notes:
   Used to set a coarse vertical origin so the hexapod operates near
   the centre of its Y travel. Standard APS optical-table hardware on
   a Vibraplane isolation base (visible in the sample-stack photo
   below).

.. figure:: ../img/sample_optical_table_y.png
   :width: 25%
   :align: center

   Single-motor stage-control screen for the Y axis (``2bmb:m24``).
   This is the screen used for routine vertical positioning of the
   sample tower — no aggregating table MEDM is needed since only one
   axis moves in normal operation.

.. figure:: ../img/sample_optical_table_4motors.png
   :width: 70%
   :align: center

   ``4Motors.adl`` MEDM for all four axes of the sample optical
   table (Y, DSX, USX, Z), shown for reference when the rarely-used
   X or Z axes need to be touched.

Hexapod_2BM
-----------

:Role: Coarse 6-DOF sample positioner
:Family: Hexapod
:Model: Aerotech HexGen HEX300-230HL hexapod (300 mm platform,
   230 mm home height). Full ordering code: HEX300-230HL with
   suffixes ``-E1`` (incremental encoders), ``-PL4`` (ULTRA
   high-accuracy performance grade) and ``-TAS`` (tested and
   integrated as a system).
:Mounted on: Sample optical table
:Carries: Sample_pitch_lam
:Driven by: ``Aerotech_Hexapod_drive`` (cora ``MotionController``
   Asset; specific product line not yet confirmed on this page)
:Degrees of freedom: X, Y, Z, A (θ\ :sub:`x`), B (θ\ :sub:`y`), C (θ\ :sub:`z`)
:Travel:
   X 55 mm, Y 60 mm, Z 25 mm, A 15°, B 15°, C 30° (single-axis moves
   from home; travels are mutually exclusive — consult Aerotech's
   HexGen workspace simulator for combined-move envelopes)
:Resolution: 20 nm (XYZ), 0.2 µrad / 0.04 arc-sec (ABC)
:Accuracy:
   ±1 µm (X), ±0.75 µm (Y, Z), ±10 µrad / ±2.1 arc-sec (A, B, C);
   PL4 ULTRA grade, measured over full travel.
:Maximum speed: 50 mm/s (X), 25 mm/s (Y, Z), 15 °/s (A, B), 30 °/s (C)
:Load capacity:
   45 kg vertical, 21 kg horizontal, 14 kg de-energized holding;
   stage mass 12 kg
:Drive: Precision ball screw, brushless slotless servo, 80 VDC bus
:EPICS: Prefix ``2bmHXP:``. Per-axis motor records (only the
   user-accessible axes are exposed):

   ===========  ==============  =====================================
   Axis         PV              Notes
   ===========  ==============  =====================================
   X            ``2bmHXP:m1``   linear, lab-X
   Y            ``2bmHXP:m2``   linear, lab-Y (vertical)
   Pitch        ``2bmHXP:m4``   rotation about lab-X
   Roll         ``2bmHXP:m5``   rotation about lab-Z (beam axis)
   ===========  ==============  =====================================

   ``2bmHXP:m3`` (Z) and ``2bmHXP:m6`` (Yaw / θ\ :sub:`z`) are not
   exposed to the user. ``m3`` is reserved for the MCTOptics IOC,
   which drives it as ``LENS_SAMPLE_Y`` for sample-side Y alignment
   relative to the microscope.

   Top-level launcher screen is the ``2bmHXP`` UI (see
   ``hexapod_01.png``); native Aerotech Ensemble interface rather
   than plain motor records.
:Notes:
   Coarse positioning of the entire sample tower. Y is shared with the
   optical table: convention is to set table Y so the sample sits in
   the beam with the hexapod at mid-travel, maximising remaining
   hexapod DOF for fine alignment. See the `Hex300-230HL data sheet
   <https://anl.box.com/s/jn2h32rqxuwmtbygilk509x41ixgsdwf>`__
   (``Hex300-Data-Sheet-D20250203.pdf``) for accuracy maps and load
   curves.

.. figure:: ../img/sample_stack.jpg
   :width: 55%
   :align: center

   2-BM-B sample stack on the Vibraplane-isolated optical table.
   Visible from bottom to top: the Aerotech HEX300-230HL hexapod (six
   struts), the Kohzu SA16A-RM laminography tilt stage (centre), and
   the Aerotech ABS250MP-M-AS air-bearing rotary at top.

Sample_pitch_lam
----------------

:Role: Laminography pitch axis
:Family: LinearStage
   (mechanically a tilt; would model as a single-axis stage in cora.
   Not yet registered in cora — the closest pending entry is
   "Broader sample-stage motors" under the Pending table at
   ``docs/deployments/2-bm/assets.md``. Consider a dedicated
   ``TiltStage`` Family if more tilt axes appear.)
:Model: Kohzu SA16A-RM goniometer / tilt stage
:Mounted on: Hexapod_2BM
:Carries: Aerotech_ABRS_rotary (via a fixed -10° wedge)
:Travel: 0° to 20° (full mechanical range; see operating convention below)
:EPICS: ``2bmb:m49``
:Notes:
   Inserted between the hexapod and the rotary specifically for
   laminography. A fixed **-10° wedge** sits between this stage and
   the rotary; the stage is held at **+10°** so the two cancel and
   the rotation axis is vertical (standard tomography). Sweeping the
   stage between 0° and 20° therefore gives a **±10° (20° total)**
   range of net rotary-axis tilt for laminography. The default
   laminography setpoint is **+15°** on the stage, i.e. +5° net
   rotary-axis tilt.

Aerotech_ABRS_rotary
--------------------

:Role: Sample rotation axis (theta)
:Family: RotaryStage
:Model:
   Stage — Aerotech ABS250MP-M-AS air-bearing direct-drive rotary
   (250 mm aperture, mid-precision class). Drive — Aerotech Ensemble
   HLE10-40-A-MXH (HLe-series digital drive). The cora Device
   identifier ``Aerotech_ABRS_rotary`` is retained for stability of
   downstream references even though the installed hardware is the
   ABS250MP, not an ABRS.
:Mounted on: Sample_pitch_lam (via a fixed -10° wedge — see above)
:Carries: Sample_top_X, Sample_top_Z
:Driven by: ``Aerotech_Ensemble_drive`` (cora ``MotionController``
   Asset wrapping the Aerotech Ensemble HLE10-40-A-MXH)
:Travel: -360 deg to +360 deg
:Max speed: 720 deg/s
:Encoder resolution: 0.0001 deg
:Homing offset: 0 deg
:EPICS: ``2bmb:m102``
   (PV mapping from
   `tomoScanStream.substitutions
   <https://github.com/tomography/tomoscan/blob/master/iocBoot/iocTomoScanStream_2BMB/tomoScanStream.substitutions>`__,
   where ``ROTATION = 2bmb:m102``)
:Notes:
   The two Sample_top_* stages above this axis (Sample_top_X and
   Sample_top_Z) co-rotate with theta. In projection geometry their
   effect is in the rotating frame, not the lab frame.

Sample_top_X
------------

:Role: Fine sample translation perpendicular to the beam
   (co-rotates with theta). Operationally the "0/180 stage" —
   motion lies along the beam when theta = 0° or 180°.
:Family: LinearStage
:Model: Kohzu CYAT-070 crossed-roller alignment stage,
   80 × 80 mm table, ball-screw lead 1.0 mm. See
   :doc:`../ops/item_050` for the operational page.
:Mounted on: Aerotech_ABRS_rotary
:Carries: (sample)
:Travel: ±15 mm
:Resolution: 1 / 0.5 / 0.05 µm (full / half / 1/20 microstep)
:Max speed: 5 mm/s
:Repeatability: ≤±0.5 µm
:Lost motion: ≤2 µm
:Backlash: ≤1 µm
:Straightness: ≤3 µm / 30 mm (horizontal and vertical)
:Load capacity: 98 N (10 kgf)
:Weight: 1.7 kg
:EPICS: ``2bmb:m18``
   (matches ``LENS_SAMPLE_X`` in
   ``iocBoot/iocMCTOptics/mctOptics.substitutions`` — this is the
   sample-side X motor MCTOptics drives for lens/sample alignment.)
:Driven by: ``OMS_VME58_2bmb_drive`` (cora ``MotionController``
   Asset wrapping the OMS-VME58 card in ``ioc2bmb`` that hosts
   the entire ``2bmb:m1``–``m91`` motor band)

Sample_top_Z
------------

:Role: Fine sample translation along the beam (co-rotates with
   theta). Operationally the "90/270 stage" — motion lies along
   the beam when theta = 90° or 270°.
:Family: LinearStage
:Model: Kohzu CYAT-070 crossed-roller alignment stage
   (same hardware as Sample_top_X; see :doc:`../ops/item_050`).
:Mounted on: Aerotech_ABRS_rotary
:Carries: (sample)
:Travel: ±15 mm
:Resolution: 1 / 0.5 / 0.05 µm (full / half / 1/20 microstep)
:Max speed: 5 mm/s
:Repeatability: ≤±0.5 µm
:Lost motion: ≤2 µm
:Backlash: ≤1 µm
:Straightness: ≤3 µm / 30 mm (horizontal and vertical)
:Load capacity: 98 N (10 kgf)
:Weight: 1.7 kg
:EPICS: ``2bmb:m17``
   (matches ``LENS_SAMPLE_Z`` in
   ``iocBoot/iocMCTOptics/mctOptics.substitutions`` — this is the
   sample-side Z motor MCTOptics drives for lens/sample alignment.)
:Driven by: ``OMS_VME58_2bmb_drive`` (cora ``MotionController``
   Asset; same as ``Sample_top_X``)


Detector system
===============

The 2-BM-B detector is an **Optique Peter MICRX080 white-beam triple-
objective microscope**, mounted on a 1 m linear Z stage that itself
sits on a dedicated APS-standard optical table. The Z stage moves the
entire microscope along the beam from near-contact with the sample
(short propagation) out to ~1 m for phase-contrast imaging; the table
is used to keep the detector centred on the beam as Z varies.

Kinematic chain (top of beam down to floor)::

   FLIR Oryx 5MP  /  FLIR Oryx 31MP               (two cameras, selected via folding mirror)
     +-- Camera selector stage                    (Schunk LPTM 30, two-position mirror)
          +-- Dual-port system + tube lens
               +-- Triple-objective head          (3 microscope heads, Mitutoyo MPLAPO)
                    +-- Objective selector       (Nanotec ST4118M1404-B + ERO 1420 coder)
                         +-- Scintillator support (LuAG, tiltable)
                              +-- Optique Peter MICRX080 microscope body
                                   +-- Optique Peter 1 m linear Z stage  (along beam)
                                        +-- Detector optical table       (X / Y / Z / roll / pitch / yaw)
                                             +-- Hutch floor

cora's ownership view differs from the kinematic-mounting view above:
the lens turret and the Optique Peter Z stage are registered as
Device-level siblings under the ``2-BM`` Unit, then wired into the
``MCTOptics`` Component via ``Plan.wiring`` rather than nested under
it. The objectives, cameras, and scintillator are children of
``MCTOptics`` in cora. See ``docs/deployments/2-bm/assets.md`` for the
canonical composition.


Optique Peter MICRX080 microscope
---------------------------------

:Role: White-beam triple-objective indirect-detection microscope
   (~55 m from source)
:Family: Microscope
:Model: Optique Peter **MICRX080**, ANL configuration
   (manual MAN-11863-0521-0465-A, 21/05/2021)
:Configuration:
   Three microscope heads, each accepting one Mitutoyo MPLAPO long-
   working-distance objective; an in-beam objective selector translates
   the chosen head onto the optical axis. The dual-port system splits
   the optical path between two cameras via a switchable folding-
   mirror "camera selector". A common filter and per-head individual
   filter live above the objectives; a tiltable scintillator support
   sits below them.
:Cameras:
   Two cameras on the dual-port system (current ANL configuration):

   - **FLIR Oryx 5MP** (camera 0, ``2bmSP1:`` areaDetector prefix).
   - **FLIR Oryx 31MP** (camera 1, ``2bmSP2:`` areaDetector prefix).

   The cameras shipped in the manual's optical table (PCO Dimax HS
   and Adimec Quartz Q-12A180) have been replaced; the manual's §16
   table is still informative for object-field / oversampling
   estimates if you substitute the Oryx pixel size and sensor format.
   cora now records the **PCO Dimax HS** under the Decommissioned
   block of ``docs/deployments/2-bm/assets.md`` with rationale
   "superseded by the FLIR Oryx detector chain" (per cora's #89).
   cora's ``Camera`` Family schema is now made explicit at 2-BM
   with ``max_framerate_hz``, ``sensor_kind``, and ``readout_mode``
   fields so the high-framerate Dimax and the general-purpose Oryx
   share one Family (the variant-as-settings rule, not variant-
   as-subtype).
:Objectives:
   Current ANL configuration, three slots:

   - Lens 0 — **1.1×**
   - Lens 1 — **2×**
   - Lens 2 — **10×**

   All Mitutoyo MPLAPO long-working-distance class. The manual lists
   the broader objective family the microscope supports (2× / 5× /
   5×HR / 7.5× / 10× / 20×) with F200 mm tube lens and 30 mm best
   image circle.
:Objective selector:
   - Stepper motor: **Nanotec ST4118M1404-B**, 1.8°/step (200 steps/rev),
     bipolar, 1.7 VDC, 1.4 A/phase.
   - Coder: **Heidenhain ERO 1420**, 1250 lines/rev, TTL, 5 V.
   - Drive: ball screw, 2 mm/rev pitch, direct mounting.
   - Travel: nominal 60 mm between adjacent objectives.
   - Closed-loop reference: left-end precision switch (±1 µm
     reproducibility) or zero-coder mark.
   - ANL-MICRX080 calibrated objective positions (mm from left
     precision end switch): A=2.3006, B=0.5625, C=59.6835, D=59.6101
     (see §13.3.1 of the manual).
:Camera selector:
   - Stage: **Schunk LPTM 30** with two folding mirrors.
   - Stepper motor: 200 steps/rev full step, 0.5 mm spindle pitch,
     max 2.5 mm/s.
   - Positioning error <35 µm/100 mm; repeatability <6 µm (uni) /
     <9 µm (bi).
:Per-objective focus:
   Each microscope head has its own motorised focus stage, so the
   three objectives focus independently.
:Scintillator support:
   Tiltable square-scintillator support (8×8 mm or 12×12 mm), with a
   ring-mounted variant (25×25 mm) for the 1× head. Vitreous-carbon
   protective window; spring-loaded mount (see §6 of the manual).
:Mounted on: Optique Peter 1 m linear Z stage
:Dimensions: ~338 × 561 × 169 mm with camera-protection box;
   ~332 × 530 × 347 mm without

.. note::

   This page summarises the microscope as built — for installation,
   alignment, scintillator changes, focus calibration, and pinouts
   refer to the full manual (53 pages) at
   ``MAN-11863-0521-0465-A.pdf``.


MCTOptics — Optique Peter IOC
-----------------------------

:Role: EPICS interface that exposes the Optique Peter microscope
   (objective + camera selectors, per-head focus, per-camera rotation,
   sample-side alignment) as a single high-level API. Implements the
   sequencing required so that selecting a lens or camera moves the
   underlying motors to the calibrated positions and applies the
   per-combination offsets.
:Family: Microscope-IOC (composite)
:Repository: https://github.com/xray-imaging/mctoptics
   (local checkout: ``/Users/decarlo/conda/mctoptics-decarlof/``)
:Documentation: https://mctoptics.readthedocs.io
:Prefix: ``2bm:MCTOptics:``
:Top-level operator PVs:
   - ``2bm:MCTOptics:LensSelect`` — mbbo, ``Pos. 0`` / ``Pos. 1`` /
     ``Pos. 2`` (the three objective slots).
   - ``2bm:MCTOptics:CameraSelect`` — mbbo, ``Pos. 0`` / ``Pos. 1``
     (Adimec / Dimax, via the folding-mirror camera selector).
   - ``2bm:MCTOptics:LensSelected``, ``CameraSelected`` — status
     readbacks (also report intermediate "Moving between …" state).
   - ``2bm:MCTOptics:LensName{0,1,2}``, ``CameraName{0,1}`` — string
     labels (mirror into the ``LensSelect`` / ``CameraSelect`` choice
     strings on init).
   - ``2bm:MCTOptics:ScintillatorType``, ``ScintillatorThickness``,
     ``CameraObjective``, ``CameraTubeLength``, ``ImagePixelSize``,
     ``DetectorPixelSize`` — optics metadata stamped into each scan.
   - ``2bm:MCTOptics:CameraBinning`` (``1x`` / ``2x`` / ``4x``) and
     ``Camera{0,1}Bit`` (``8`` / ``10`` / ``12`` / ``16-bit``).
   - ``2bm:MCTOptics:Cut{Left,Right,Top,Bottom}`` + ``Cut`` (busy)
     for image cropping.

Underlying motor map (from ``iocBoot/iocMCTOptics/mctOptics.substitutions``):

===========================  ======================  ================================
Macro                        PV                      Purpose
===========================  ======================  ================================
``LENS_MOTOR``               ``2bmb:m1``             Objective selector (turret)
``CAMERA_MOTOR``             ``2bmb:m5``             Camera selector (folding mirror)
``LENS0_FOCUS``              ``2bmb:m2``             Objective #0 focus
``LENS1_FOCUS``              ``2bmb:m3``             Objective #1 focus
``LENS2_FOCUS``              ``2bmb:m4``             Objective #2 focus
``CAM0_ROT``                 ``2bmb:m7``             Camera 0 rotation
``CAM1_ROT``                 ``2bmb:m8``             Camera 1 rotation
``LENS_SAMPLE_X``            ``2bmb:m18``            Sample alignment in X
``LENS_SAMPLE_Y``            ``2bmHXP:m3``           Sample alignment in Y (hexapod)
``LENS_SAMPLE_Z``            ``2bmb:m17``            Sample alignment in Z
``CAMERA0``                  ``2bmSP1:``             Camera-0 areaDetector prefix
``CAMERA1``                  ``2bmSP2:``             Camera-1 areaDetector prefix
``TOMOSCAN``                 ``2bmb:TomoScan:``      Linked TomoScan IOC
===========================  ======================  ================================

All eight ``2bmb:m1``–``m8`` motors in this map plus ``m17``
and ``m18`` are slots on the same OMS-VME58 card — cora Asset
``OMS_VME58_2bmb_drive``. The hexapod motor ``2bmHXP:m3`` is one
DoF of ``Hexapod_2BM``, driven by ``Aerotech_Hexapod_drive``.

Calibrated lens positions (mm, both cameras): Pos. 0 = -60.030,
Pos. 1 = -0.8370, Pos. 2 = 58.64. Camera positions: Pos. 0 = 20,
Pos. 1 = 15. Per-objective and per-camera fine focus and rotation
offsets are held in the IOC's autosave file.

.. figure:: ../img/optique_peter_medm.png
   :width: 70%
   :align: center

   MCTOptics operator screen. Top: server status and the
   ``sync to motors`` button. Middle: the three lens slots
   (Lens 0 = 1.1×, Lens 1 = 2×, Lens 2 = 10×), each with its own
   focus value. Right: the two cameras (Camera 0 = Oryx 5MP,
   Camera 1 = Oryx 31MP) with their rotation offsets. The
   ``Default`` button restores the per-combination calibrated
   focus / rotation values from the IOC's autosave file.

Optique_Peter_focus_Z
---------------------

(cora Asset identifier; previously called "Optique Peter Z stage"
in this page.)

:Role: Carries the entire microscope body along the beam from
   near-contact with the sample out to ~1 m for phase-contrast
   imaging.
:Family: LinearStage
:Model: Aerotech **PRO225SL-1000** mechanical-bearing linear stage
   (SL precision class, 1000 mm travel; the longest member of the
   PRO225SL family). cora Model: ``aerotech_pro225sl_1000``.
:Mounted on: Detector optical table
:Carries: Optique Peter MICRX080 microscope
:Driven by: ``Aerotech_2bmbAERO_drive`` (cora ``MotionController``
   Asset wrapping the Aerotech drive that the ``2bmbAERO`` IOC
   manages)
:Travel: 1000 mm
:Accuracy: ±18 µm (SL Standard; calibrated grade not offered above 500 mm)
:Resolution: 0.1 µm (high-resolution feedback) / 1.0 µm
:Repeatability: ±1 µm bidirectional
:Straightness: ±9.5 µm horizontal and vertical
:Angular error: 110 µrad pitch, roll, and yaw
:Max speed: 140 mm/s (1000 mm variant; shorter PRO225SL travels reach 220 mm/s)
:Load capacity: 100 kg horizontal, 60 kg vertical (axial), 100 kg side
:Moving mass: 7.3 kg (with tabletop)
:Stage mass: 40.9 kg (without motor)
:Material: anodised aluminium
:MTBF: 20,000 h
:EPICS: ``2bmbAERO:m1`` (motor record; the table-level VAL field is
   ``2bmbAERO:m1.VAL``). Lives in a dedicated Aerotech IOC,
   ``2bmbAERO``, separate from the main ``2bmb`` IOC.


Detector optical table
----------------------

:Role: Floor-referenced support for the Optique Peter Z stage and the
   microscope; used to keep the detector centred on the beam as the Z
   stage moves.
:Family: OpticalTable
:Mounted on: Hutch floor
:Carries: Optique Peter Z stage (and the microscope)
:Degrees of freedom: X, Y, Z, AX (roll), AY (pitch), AZ (yaw) —
   six virtual axes computed from six underlying support motors.
:Geometry: ``SRI`` (Sector Research Instrumentation: 3 Y supports,
   2 X supports, 1 Z support — 6 motors total).
:EPICS: Virtual table record ``2bmb:table3`` (composite). Loaded in
   the 2-BM-B IOC by
   ``dbLoadRecords("$(DIR)/table.db", "P=2bmb:,Q=Table3,T=table3,
   M0X=m13,M0Y=m14,M1Y=m12,M2X=m10,M2Y=m9,M2Z=m11,GEOM=SRI")``.

Underlying motor map:

=======  ============  ================================
Macro    Motor PV      Role on the table
=======  ============  ================================
``M0X``  ``2bmb:m13``  corner 0 — X support
``M0Y``  ``2bmb:m14``  corner 0 — Y support
``M1Y``  ``2bmb:m12``  corner 1 — Y support (no X here)
``M2X``  ``2bmb:m10``  corner 2 — X support
``M2Y``  ``2bmb:m9``   corner 2 — Y support
``M2Z``  ``2bmb:m11``  corner 2 — Z support (single Z)
=======  ============  ================================

The ``table.db`` template combines these into composite translate /
rotate axes ``2bmb:table3.X``, ``.Y``, ``.Z``, ``.AX``, ``.AY``,
``.AZ``, plus per-leg readbacks under the ``2bmb:table3:`` prefix.

.. figure:: ../img/optique_peter_table_medm.png
   :width: 60%
   :align: center

   ``table_full.adl`` MEDM screen for ``2bmb:table3`` (the detector
   optical table under the Optique Peter instrument). The Translate
   and Rotate columns are calc-driven composites; the Motors column
   shows the six underlying motor records listed above.


Trigger and synchronisation
===========================

The Aerotech Ensemble rotary controller emits a position-synchronised
output (PSO) pulse every fixed number of encoder counts of the sample
rotation. Those pulses are not wired straight to the detector — they
pass through a **softGlueZynq** FPGA that conditions them (pulse
width and delay), can mask them through a programmable lookup
pattern, and only then drives the camera trigger input.

softGlueZynq (PSO → camera trigger)
-----------------------------------

:Role: Conditions and gates the Aerotech rotary PSO pulse train into
   the detector trigger input. Provides programmable pulse width and
   delay, a 2:1 MUX between raw PSO and a software-defined custom
   pattern (``trigILF``), and a ``memPulseSeq`` block for arbitrary
   interlaced sequences.
:Family: ``TimingController``
   (cora's Pending entry ``softGlueZynq_FPGA`` carries Family
   ``TimingController`` — the second ``<Domain>Controller`` Family
   after ``MotionController``. Affordance is ``Pulsing`` via the
   ``Controller`` Role: the timing box is itself the actor, not a
   driven device. Substrate "FPGA" is not a Family axis; the
   ``TriggerFPGA`` placeholder we previously cited has been
   superseded by ``TimingController`` per cora's #89.)
:Hardware: APS softGlueZynq — Xilinx Zynq SoC (FPGA + ARM) on a
   MicroZed-class carrier. The EPICS IOC runs on the ARM core and
   starts automatically at boot.
:Mounted on: Hutch electronics rack
:Inputs: PSO output of the Aerotech Ensemble drive that runs the
   rotary ``2bmb:m102``.
:Outputs: Camera trigger input. Per
   ``tomoscan/tomoscan_fpga_2bm.py``, the wiring is FLIR Oryx
   ``Line2`` for the current Oryx 5MP / 31MP cameras; the same
   module also supports Grasshopper3 (``Line0``) and Adimec
   (continuous-timed) modes if those cameras are swapped in.
:Signal path:
   PSO → ``MUX2-1`` (selects raw PSO or ``trigILF``) →
   ``GateDly 1`` (pulse width / delay; default 100 × 100 ns = 10 µs)
   → camera trigger.
:EPICS prefix: ``2bmbMZ1:SG:``. Key PVs:

   ===================================  =========================================================
   PV                                   Purpose
   ===================================  =========================================================
   ``2bmbMZ1:SG:MUX2-1_SEL_Signal``     Selects raw PSO (0) or ``trigILF`` (1) onto the trigger.
   ``2bmbMZ1:SG:memPulseSeq.enable``    Arms (1) / disarms (0) the custom-pattern playback.
   ``2bmbMZ1:SG:GateDly1.DLY``          Delay from input edge to start of output pulse.
   ``2bmbMZ1:SG:GateDly1.Width``        Output pulse width, in 10 MHz clock cycles (100 = 10 µs).
   ===================================  =========================================================

:IOC location: ``/net/s2dserv/xorApps/epics/synApps_SG/ioc/2bmbMZ1/``
   on ``arcturus``. Start with ``./start_epics_2bmbMZ1`` (or the
   matching ``./start_caQtDM_2bmbMZ1`` for the operator screens).
:tomoscan integration:
   ``TomoScan2BM`` (subclass of ``TomoScanFPGAPSO``) in
   `tomoscan/tomoscan_fpga_2bm.py
   <https://github.com/decarlof/tomoscan/blob/master/tomoscan/tomoscan_fpga_2bm.py>`__
   selects the camera-specific trigger mode
   (``set_trigger_mode_oryx`` / ``_grasshopper`` / ``_adimec``);
   the base ``TomoScanFPGAPSO`` class drives the PSO configuration
   on the Aerotech controller.
:Custom pulse patterns:
   For interlaced-fly tomography, pulse subsets are loaded with the
   helper ``macros_ILF.write_PSO_array`` (e.g.
   ``write_PSO_array([0, 2, 4, 6])`` triggers only on PSO edges 0,
   2, 4, 6). See
   `interlaced/fpga/macros_ILF.py
   <https://github.com/decarlof/interlaced/blob/main/fpga/macros_ILF.py>`__.
:Notes:
   :doc:`../ops/item_060` is the operational page for the FPGA —
   MEDM screens, how to set ``DLY`` and ``Width``, how to flip the
   MUX, and the ``memPulseSeq`` workflow.


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
