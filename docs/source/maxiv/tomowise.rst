================================
TomoWISE — Beamline components
================================

Reference inventory of the physical hardware that will make up the
TomoWISE beamline at MAX IV, walked from the source to the detector.
Source for everything below is the **TomoWISE Technical Design
Report** dated 2025-03-31 (main applicant: Olof Karis, MAX IV).

.. warning::

   **Under construction.** TomoWISE is in the design phase. Every
   number below is the *design specification* from the TDR, not a
   commissioned measurement. PV names are TBD — MAX IV uses
   Tango / Sardana, not EPICS, so the eventual control surface will
   look different from the 2-BM pages.


Overview
========

TomoWISE will sit at straight section 7 of the MAX IV 3 GeV ring,
fully dedicated to high-resolution full-field tomography of
materials in the 20–65 keV range. Two independent insertion devices
share the 4.5 m straight, feeding three optical schemes and two
endstations (microtomography at ~45 m, nanotomography at 49–51 m).

Walk from source to detector (z values are distance from the centre
of the straight section, mm)::

   Storage ring straight section 7
     -> CPMU14 + 3T3PW                  (two sources, 4.5 m straight)
     -> Front End                       (z ≈ 5–17 m: FM1, FM2, MSM, HA)
     -> Optics hutch                    (z ≈ 22.5–32.5 m)
          DM1 (BM + CVD + WBS + BPM1)
          -> PFU (Si power filters)
          -> MLM (multilayer monochromator)
          -> DM2 (white-beam stop, MBS, BPM2)
          -> Safety unit (HA, SS1, SS2)
     -> Microtomography endstation       (z ≈ 45 m; rotary, sample table, slits, fast shutter)
     -> Nanotomography endstation        (z ≈ 49–51 m; KB mirror pair + sample stage)
     -> Detector gantry                  (shared, on 7 m rails between endstations)


Beam delivery
=============

Insertion devices
-----------------

Two complementary sources share the 4.5 m straight. They are
operated **independently** (radiated power and beam footprint
differ enough that simultaneous operation is not allowed).

CPMU14 — Cryogenic Permanent Magnet Undulator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Primary source for high-flux microtomography (with or
   without MLM) and for nanotomography (with KB).
:Family: InsertionDevice
:Type: in-vacuum cryogenic permanent magnet undulator (Pr₂Fe₁₄B
   magnets, vanadium permendur poles, cooled to 77 K)
:Period: 14 mm
:Length: 2.0 m (cryostat 2.67 m)
:Minimum gap: 3.8 mm
:Maximum K_eff: 1.84
:Power at 500 mA: 11.1 kW
:RMS phase error: < 3°
:Position in straight: -505 mm from centre
:Beam divergence: ~100 µrad
:Beam size at sample (45–48 m): ~1.5 × 2.4 mm² to 4.5 × 4.5 mm²
:Flux:
   - 5 × 10¹⁶ ph/s @ 20 keV (1 × 2.4 mm² beam, ΔE/E = 1 × 10⁻²)
   - 3 × 10¹⁶ ph/s @ 33 keV (2 × 2.4 mm², MLM)
   - 1 × 10¹⁵ ph/s @ 63 keV (2 × 2.4 mm², MLM)
   - 4 × 10¹⁶ ph/s broadband centred at 55 keV (CVD + PF + MF)

3T3PW — 3-Tesla 3-Pole Wiggler
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Wide-field large-FOV microtomography (broadband white beam,
   no MLM, no KB).
:Family: InsertionDevice
:Type: out-of-vacuum 3-pole wiggler (similar to the device
   recently commissioned at SESAME)
:Field: 3 T
:Magnetic length: 412 mm (occupies 0.7 m of the straight)
:Minimum gap: 11 mm
:Position in straight: +1734 mm from centre
:Beam divergence: 1 mrad (h) × 0.1 mrad (v)
:Beam size at sample: 45 × 4.5 mm² (h × v)
:Flux: ~10¹⁵ ph/s @ 25–65 keV broadband (CVD filter only)
:Total radiated power: 1.6 kW at 500 mA


Front End
---------

The Front End is shared by both insertion devices and must safely
switch its acceptance between the narrow CPMU14 beam (100 µrad)
and the wide 3T3PW beam (1 mrad horizontal). Layout, source to
downstream::

   CPMU + 3T3PW -> FM1 -> FM2 -> MSM -> HA -> MM1 -> MM2

Element distances and absorbed power (from TDR Table 7.1, distance
from CPMU14):

=============  =========  ============  ============  =========================
Element        z [mm]     Opening X     Opening Y     Power absorbed
=============  =========  ============  ============  =========================
CPMU14         0          —             —             source (11.3 kW out)
Dipole mask    5767       1.977 mrad    1.977 mrad    —
Reference      10000      3.000 mrad    3.000 mrad    —
FM1            14000      1.100 mrad    1.100 mrad    200 W
FM2            15000      1.100 mrad    0.100 mrad    6.9 kW
MSM (wide)     16500      1.100 mrad    0.100 mrad    0 W (4.2 kW out)
MSM (narrow)   16500      0.100 mrad    0.100 mrad    3.33 kW (0.82 kW out)
Heat absorber  17000      1.176 mrad    0.706 mrad    0.82 kW (0 kW out)
=============  =========  ============  ============  =========================

FM1 — first Fixed Mask
~~~~~~~~~~~~~~~~~~~~~~

:Role: 1 × 1 mrad² absorbing aperture (calculated from the 3T3PW
   centre); MAX IV standard design.
:Family: BeamMask
:Absorbs: ~2 kW

FM2 — second Fixed Mask
~~~~~~~~~~~~~~~~~~~~~~~

:Role: 1.1 × 0.1 mrad² acceptance — transmits the wide 3T3PW
   horizontal beam while clipping the vertical.
:Family: BeamMask
:Absorbs: up to 7 kW from the CPMU14 source

MSM — Movable Safety Mask
~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: PSS-grade switchable mask — provides either the wide
   (1.1 mrad horizontal) or narrow (0.1 mrad horizontal) acceptance
   matched to the selected insertion device. Interlocked to the
   CPMU14 gap so the device cannot close to its minimum gap unless
   the mask is in the safe (narrow) position.
:Family: BeamMask (PSS-grade)
:Mechanism: pneumatic actuator with PSS-grade switches and hard
   stops; mm-accuracy positioning, <100 µm lateral parasitic motion
:FEA-predicted distortion: ~100 µm on the long edge under CPMU14
   at minimum gap — <1 % effect on effective opening
:Aperture (wide / narrow): 1.1 × 0.1 mrad² / 0.1 × 0.1 mrad²

Heat Absorber + Movable Masks (HA, MM1, MM2)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: HA protects the Safety Shutter (4 kW absorption budget for
   worst-case interlock fault). MM1/MM2 are MAX IV standard
   movable masks with increased horizontal travel to match the
   1 mrad 3T3PW acceptance — used to fine-tune downstream
   acceptance.
:Family: BeamMask


Beamline optics (optics hutch, z = 22.5–32.5 m)
-----------------------------------------------

All optical elements with their longitudinal positions from the
straight-section centre (TDR Table 8.1):

==============================  ======  ============  ========================================
Component                       Short   Distance [m]  Comment
==============================  ======  ============  ========================================
FE trigger unit                 FE      23.00         —
Diagnostic Module 1             DM1     —             beam-defining mask + collimator
Fixed Mask 1                    FM1     23.61         —
Bremsstrahlung Collimator       BC1     23.85         —
Diamond Filter                  CVD     24.05         0.35 mm CVD diamond, water-cooled
White-beam slits                WBS     24.28         water-cooled Cu, 60 µrad opening
Beam Position Monitor 1         BPM1    24.57         X-ray BPM
Power Filters unit              PFU     25.20         two Si wedges (see PFU block)
Multilayer Monochromator        MLM     25.90         two W/Si (or W/B₄C) bilayers, d = 2.5 nm
Diagnostic Module 2             DM2     —             —
White-beam stop                 WBS2    27.95         used when MLM is in beam
Bremsstrahlung Collimator       BC2     28.06         —
Monochromatic Beam Slits        MBS     28.28         —
Beam Position Monitor 2         BPM2    28.53         X-ray BPM
Safety unit                     SU      31.83         HA + SS1 + SS2
Experimental hutch wall         —       32.50         —
==============================  ======  ============  ========================================

DM1 / DM2 — Diagnostic modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: DM1 hosts the Beam-defining Mask (BM, water-cooled Cu with
   60 µrad opening), bremsstrahlung collimator, white-beam slits,
   and BPM1. DM2 hosts the white-beam stop (blocks transmitted beam
   when MLM is in use), monochromatic beam slits, and BPM2.
:Family: Diagnostics

CVD diamond filter
~~~~~~~~~~~~~~~~~~

:Role: First filter in the optics hutch — reduces heat load on
   everything downstream.
:Family: Filter
:Material: CVD diamond, 0.35 mm thick
:Frame: 35 × 8 mm² plate on a copper frame with 30 × 3 mm² aperture
:Cooling: thermal contact to the copper frame, thermocouple monitor
:Transmission: 95 % @ 20 keV, 50 % @ 10 keV, ≈ 0 % for lowest
   CPMU14 harmonics

PFU — Power Filters Unit
~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Tunable Si wedge attenuator — fine spectrum / flux control
   without engaging the MLM.
:Family: Filter
:Mechanism: two identical water-cooled Si wedge crystals on
   vertical translation stages; wedge angle 3°, thickness varies
   from 6 mm to 0.2 mm along the wedge.
:Dimensions: a = 7 mm, b = 6 mm, c = 10 mm, d = 120 mm, e = 140 mm
:Effective thickness range: 0.2 to 25 mm
:Attenuation: up to 80 % at 65 keV, 95 % at 45 keV

Metal Filter unit (MF)
~~~~~~~~~~~~~~~~~~~~~~

:Role: Third filter stage at the start of the experimental hutch.
   Tunes X-ray transmission from 10⁻⁴ to 1 across the operational
   range by combining Fe and Cu plates of different thicknesses.
:Family: Filter
:Design: NanoMAX-style carriages with multiple Fe / Cu plate slots
:Mounted on: experimental-hutch end-wall

MLM — Multilayer Monochromator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Horizontally-deflecting two-bounce multilayer monochromator
   covering 20–65 keV. CPMU14 only — designed so the mirrors can
   be translated out of the way for the wide 3T3PW beam.
:Family: Monochromator
:Geometry: horizontal Bragg, fixed pitch (Bragg-angle scan via
   rotation), 500 mm longitudinal separation between mirrors,
   lateral offset on 2nd crystal (up to 8.55 mm) to track energy
:Coating: W/Si (or W/B₄C) bilayers, d = 2.5 nm, 140 bilayers
:Substrate: 40 × 20 × 320 mm³ (x, z, y) Si crystals
:First-mirror distance: 25.9 m from source
:Max beam at 1st mirror (@ 44 µrad): 300 × 1 mm² (h × v)
:Max power on 1st mirror: 100 W
:Energy range: 20 – 65 keV
:Energy bandwidth: ΔE/E ≈ 1.8 %
:Bragg-angle range: 3.84 – 12.6 mrad
:Slope error: 0.05 µrad
:Surface roughness: < 0.15 nm RMS
:Bragg resolution: 0.5 µrad
:Motors (TDR Table 8.4):

   ==========  ============  ===============  =============  ===================================
   Axis        Range         Resolution       Repeatability  Function
   ==========  ============  ===============  =============  ===================================
   Pitch (Ry)  0 – 20 mrad   < 8 µrad         < 2 µrad       Bragg angle → energy
   Ty          ±5 mm         < 10 µm          < 10 µm        Crystal-in-beam optimisation
   Tx          +3 / −15 mm   < 5 µm           < 5 µm         Chamber lateral; carries C2x
   C2x         0 – 15 mm     < 5 µm           < 5 µm         2nd-crystal lateral (energy track)
   C2_pitch    2 mrad        —                —              Fine pitch of 2nd crystal
   ==========  ============  ===============  =============  ===================================

KB pair — Kirkpatrick-Baez focusing mirrors (nano-tomography)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Source for cone-beam projection microscopy at the
   nanotomography endstation. Demagnifies the source spot to
   ~200 nm so the sample can sit downstream of the focus.
:Family: Mirror (KB pair)
:Type: fixed-curvature graded multilayer mirrors (two pairs side
   by side — one optimised for 30 keV, one for 45 keV)
:Mounted on: KB vacuum chamber at the entrance of the
   experimental hutch (~48.85 / 49.00 m from source)
:Substrate: VFM 175 × 20 × 40 mm³; HFM 90 × 20 × 40 mm³
:Substrate shape: elliptical cylinder
:Mirror-centre-to-focal-point: VFM 210 mm; HFM 60 mm
:Grazing angle (30 keV / 45 keV): VFM 7.49 / 4.27 mrad; HFM 0.50 / 0.285 mrad
:Multilayer period (30 keV / 45 keV): VFM 20 / 27.6 / 31.5 Å;
   HFM 20 / 48.35 / 56.3 Å
:Focal spot (h × v): 205 × 196 nm @ 30 keV; 196 × 80 nm @ 45 keV
:Numerical aperture (h × v): 5.5 × 2.8 mrad @ 30 keV;
   4.0 × 2.5 mrad @ 45 keV
:Total flux at focus: 1.29 × 10¹³ ph/s @ 30 keV;
   2.77 × 10¹² ph/s @ 45 keV
:Power at focus: 72 mW @ 30 keV; 20 mW @ 45 keV


Safety Shutters (SS1, SS2) and Heat Absorber
--------------------------------------------

:Role: SU = Heat Absorber + two Safety Shutters at the end of the
   optics hutch, before the experimental-hutch wall. SS1 / SS2
   gate the beam between optics and experiment hutches; HA absorbs
   worst-case interlock-fault power so the shutters stay within
   their thermal budget.
:Family: Shutter
:HA thermal budget: 4 kW
:Position: 31.83 m (Safety unit) / 32.50 m (experimental hutch wall)


Operation modes
---------------

Pre-defined optical configurations (TDR Table 8.7 / 8.8):

============================================  ========  =================  =============  ===========
Imaging mode                                  Source    Filters            Monochromator  KB focusing
============================================  ========  =================  =============  ===========
Standard / high-throughput microtomography    CPMU14    < Si 1 mm          MLM            no
High speed, <1 µm pixel, 1–2 mm FOV           CPMU14    none → Si+metals   MLM → none     no
High speed, >1 µm pixel, large FOV            3T3PW     Si + metals        none           no
Large or highly attenuating samples           3T3PW     Si + metals        none           no
Nano-tomography                               CPMU14    none               MLM            yes
============================================  ========  =================  =============  ===========


Experimental stations
=====================

Microtomography endstation (z ≈ 45 m)
-------------------------------------

Two beams reach this station: the monochromatic CPMU14 beam
(1 × 2.4 mm² @ 20 keV / 2 × 2.8 mm² above 33 keV) and the broadband
3T3PW beam (45 × 4.5 mm² h × v).

Sample table
~~~~~~~~~~~~

:Role: Floor-referenced support for the rotary stage and the
   sample positioning stack. Built in-house by MAX IV; air-pad
   removable for major reconfiguration.
:Family: OpticalTable
:Position: 45 m from source, fixed
:Surface-to-beam: 390 mm
:Degrees of freedom:

   - **Xt** — sample-tower X translation (perpendicular to beam):
     ±100 mm range, 0.3 µm resolution, < 1 µm straight-line
     accuracy
   - **Yt** — sample-tower Y (vertical, imaging-height + flat-field
     offset): +50 / -150 mm range, 0.5 µm resolution, < 1 µm
     straight-line accuracy
   - **β** — tilt to align ωy rotation axis perpendicular to the
     incident beam: 1.2°, 5 mdeg resolution, 10 mdeg repeatability

Rotary stage (ω_y)
~~~~~~~~~~~~~~~~~~

:Role: Sample rotation axis — the master device that triggers all
   other devices during acquisition.
:Family: RotaryStage
:Model (target): Lab Motion Systems **RT100AX** (continuous
   rotation, slip-ring built in, TTL position output)
:Mounted on: sample table
:Travel: continuous
:Max speed: 1200 rpm (20 revolutions per second)
:Resolution: 1 mdeg
:Repeatability: 3 mdeg
:Straight-line accuracy / eccentricity: 0.03 µm (radial),
   2 mdeg, 200 nm wobble, 75 mm above stage surface
:Load capacity: 2 kg axial, 10 kg normal
:Encoder output: TTL, 3600 pulses per rotation — drives detector
   triggering downstream
:Radial / axial error motion: < 100 nm / < 50 nm

Sample positioning stage (Xs, Zs)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Fine positioning of the sample on the rotation axis.
:Family: LinearStage (pair)
:Model (target): Lab Motion Systems **XY150B-12**
:Mounted on: top of the rotary stage (co-rotates with ω_y)
:Travel: ±6 mm each axis
:Resolution: 0.1 µm
:Precision repeat: 1 µm

Laminography tilt (α)
~~~~~~~~~~~~~~~~~~~~~

:Role: Tilt stage below the rotation axis enabling laminography
   for flat samples (PCBs, slabs).
:Family: TiltStage
:Travel: 25°
:Precision repeat: 0.1°
:Resolution: 50 mdeg

Precise slits (sample-side)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Crop the beam to the chosen field of view immediately
   upstream of the sample (180 mm above the rotation axis), to
   minimise out-of-field dose.
:Family: Slits
:Reference design: JJ X-ray **IB-C50-air**
:Max opening: 50 × 5 mm (h × v)
:Slit precision: 50 µm
:Positioning range (X / Y): 20 mm / 10 mm
:Positioning precision: 50 µm

Fast shutter (sample-side)
~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Sub-frame X-ray gating, 30–50 mm upstream of the sample
   slits. Two shutters in parallel:

   - Mode 2 (high-speed CPMU14): Arinax **Colibri**, < 5 ms
     opening, 2 mm aperture.
   - Mode 1 (wide 3T3PW): Innospexion **ultrafast shutter**,
     < 10 ms opening, > 45 × 4.5 mm² aperture.
:Family: Shutter

Slip ring
~~~~~~~~~

:Role: Allows electrical / fluid connections to a continuously
   rotating sample environment (in situ / operando).
:Family: SlipRing
:Channels: 30–40
:Drive: secondary rotation stage synchronised with the precision
   rotary (up to 1000 rpm)
:Cable feedthrough: ≥ 15 mm diameter hole through the main rotary

Optional modules
~~~~~~~~~~~~~~~~

- **Horizontal-rotation loading rig** — 10 kN tension/compression
  rig (Psylotech xTS-2022.10-V3) installed in place of the standard
  vertical-rotation tower; rotates around the horizontal axis to
  image long-thin samples in a single rotation.
- **kHz tomography module** — servo-motor add-on capable of
  0.5–1 kHz rotation at the cost of 10-µm-scale spatial resolution.


Nanotomography endstation (z ≈ 49–51 m)
---------------------------------------

Cone-beam projection microscopy. Sample sits downstream of the KB
focus (in the diverging cone), spatial resolution and FOV are tuned
by sliding the sample between focus and detector. Detector gantry
extends to 52 m (hutch wall).

:Role: 200-nm-class spatial resolution via geometrical
   magnification through the KB-formed nanofocus.
:KB pair: see Beamline optics → KB pair.
:Sample stage: TBD in TDR (specification deferred to procurement);
   needs Abbe-error performance compatible with 200-nm resolution.


Detector system (shared gantry)
===============================

One detector system serves both endstations via a gantry that
rolls on 7 m long floor rails between the two stations. The
nanotomography station can host a second detector for fast
swapping; in that case only the sample moves between stations and
the detector gantry is untouched.

A removable, modular **flight tube** (1 mbar vacuum) covers the
length of the experimental hutch — terminating just upstream of the
sample slits in microtomography mode, or all the way to the KB
chamber in nanotomography mode.

Microscopes
-----------

============================================  ====================  ======  ===============================
Microscope                                    Magnification         NA       Used in
============================================  ====================  ======  ===============================
**MicLFOV** — Large field-of-view scope       1–2×                  > 0.2   Mode 1 (large samples, 3T3PW)
**MicHR** — High-resolution scope             4×, 10×, 20×          > 0.4   Modes 2 / 3 (standard / nano)
============================================  ====================  ======  ===============================

Both microscopes accept all four beamline cameras (sensors up to
60 mm diagonal).

Cameras
-------

Four CMOS-class cameras are planned. Specific models will be
chosen in the second project year — these are the design targets:

=====  =====================================================  =======================  ==========================
Cam    Description                                            Used with                Scintillator (LuAG:Ce)
=====  =====================================================  =======================  ==========================
I      16–25 Mpix, 16-bit sCMOS, medium speed (100–150 fps)   High-NA objective        7–15 µm
II     4 Mpix, 12-bit CMOS, > 50 000 fps                      High-NA objective        20 / 50 / 100 / 150 µm
III    ≈ 4 Mpix streaming, > 2 000 fps                        High-NA objective        20 / 50 / 100 / 150 µm
IV     150 Mpix, large sensor (54 × 40 mm, 3.76 µm pixel)     1× or 5–10× objective    (per microscope)
=====  =====================================================  =======================  ==========================

Note: the high-speed camera (II) must run at least 5 kHz in
full-frame and up to 100 kHz with cropped sensor. Camera IV
matches the device already procured for DanMAX.

Detector positioning stages (gantry)
------------------------------------

:Role: Carry the chosen microscope and camera between the two
   endstations and along the beam at each station.
:Family: LinearStage
:Axes:

   - **Xd** — gantry-mounted fine X translation (perpendicular to beam)
   - **Yd** — gantry-mounted fine Y translation (vertical)
   - **Zd** — gantry translation along the beam, on 7 m floor rails

:Rails: fixed on the floor, both sides of the sample-table
:Range: micro-tomography station (45 m) to hutch wall (52 m)
:Capacity: two sets of detector optics can be mounted
   simultaneously, either side-by-side (X-swappable) or in series
   along Z (semi-transparent upstream optics + downstream pickup)

Optics standard
---------------

Optique Peter is the reference vendor for the microscope optics
(same vendor as the 2-BM detector — see :doc:`../manual/item_020`,
**Optique Peter MICRX080**), though MAX IV may pick a smaller
vendor with comparable specifications. Decision in project year 2.


Trigger and synchronisation
===========================

The rotary stage (`Lab Motion RT100AX`) emits a TTL pulse train
on the encoder — **3600 pulses per revolution** — and is the master
clock for all other devices during acquisition. As of the TDR no
FPGA conditioner is specified (no equivalent of 2-BM's
softGlueZynq) — the rotary TTL output is expected to feed the
camera trigger inputs directly. This may evolve once the camera
trigger requirements are firm.


References
==========

- TomoWISE Technical Design Report, 2025-03-31, MAX IV Laboratory
  (``TomoWISE_TDR_final_revised.pdf``, 87 pages). Main applicant
  Olof Karis; co-applicants Dina Carbone, Rajmund Mokso
  (MAX IV / DTU), Stephen Hall (Lund University).
- For the existing TomoScan-protocol collaboration with DanMAX
  (Sardana/Tango ↔ EPICS comparison), see
  :doc:`danmax_tomoscan_collaboration`.
- For the 2-BM beamline reference page this template mirrors,
  see :doc:`../manual/item_020`.
