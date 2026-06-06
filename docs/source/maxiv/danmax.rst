============================
DanMAX — Beamline components
============================

Reference inventory of the physical hardware that makes up the
DanMAX beamline at MAX IV, walked from the source to the
detectors. Primary source for the values below is the **DanMAX
Detailed Design Report v2.0** (December 2017, by I. Kantor and
E. Bergbäck Knudsen, DTU, with M. R. V. Jørgensen, Aarhus
University); the public beamline page at
`maxiv.lu.se/beamlines-accelerators/beamlines/danmax/beamline-optics/
<https://www.maxiv.lu.se/beamlines-accelerators/beamlines/danmax/beamline-optics/>`__
is the fallback for items the DDR doesn't cover.

.. note::

   DanMAX is operational since 2019–2020. Values below come from
   the *design* report and may differ from the as-built / current
   commissioned state. The control system is **TANGO + Sardana**
   (motor electronics: ESRF **IcePAP**), not EPICS, so PV-style
   addresses on this page are placeholders; the operational
   surface is Sardana/TANGO attributes.


Overview
========

DanMAX sits at achromat 4 of the MAX IV 3 GeV ring, dedicated to
*in situ* / *operando* materials studies in the 15–35 keV range.
Two end-stations live in a single shared experimental hutch
(EH1): an **imaging** instrument (full-field absorption / phase
contrast / grating-based) and **PXRD** instruments (high-
resolution powder diffraction + large-sample-environment 2D
diffraction). A future second experimental hutch (EH2) is
reserved between the optics hutch and EH1.

Walk from source to detector (z values from the centre of the
storage straight section, mm, per DDR Table 3)::

   IVU16 (in-vacuum undulator)            (3.0 m magnetic length, achromat 4)
     -> Front End (Toyama)                (FE Aperture at z ≈ 19.2 m)
     -> Ratchet wall                      (z = 21.8 m)
     -> Optics hutch (OH, 9.9 m long)
          Trigger unit                    (z = 22.75 m)
          Bremsstrahlung collimator       (z = 23.17 m)
          High-pass filter (diamond)      (z = 23.52 m)
          Laue monochromator (placeholder) (z = 23.89 m)
          White beam slit 1               (z = 24.46 m)
          BV1 (diagnostics)               (z = 24.91 m)
          hDCM (Si 111, horizontal)       (z = 25.655 m)
          BV2                             (z = 26.40 m)
          hMLM (multilayer, horizontal)   (z = 27.27 m)
          White beam stop + bremss. coll. (z = 28.19 m)
          Monochromatic slit 1            (z = 28.44 m)
          BV3 + future BPM                (z = 30.55 m)
          CRL transfocator (Be lenses)    (z = 31.10 m)
          Monochromatic slit 2            (z = 31.60 m)
          Safety shutter                  (z = 32.20 m)
          OH end wall                     (z = 32.50 m)
     -> Experimental hutch EH1            (upstream wall ≈ 38.1 m)
          Imaging instrument              (sample at z ≈ 41.2 m)
          High-res PXRD instrument        (sample at z ≈ 44.8 m)
          Large-sample-env PXRD instrument (sample at z ≈ 48.2 m)
          Far-field detector positions    (8–10 m downstream of sample)


Beam delivery
=============

Insertion device — IVU16
------------------------

:Role: Primary photon source. Optimised for maximum flux at the
   high-energy end (~35 keV); spectroscopy-style spectral purity
   is not a design driver.
:Family: InsertionDevice
:Type: In-vacuum permanent-magnet undulator (room temperature)
:Vendor: **Hitachi** (contract July 2016; delivery January 2018)
:Magnet pole material: NdFeB
:Pole material: Vanadium Permendur
:Period length: 16 mm
:Number of periods: 187
:Magnetic length: ~3.0 m
:Magnetic gap range: 4.0 – 50.0 mm
:Minimum physical gap: 3.8 mm
:Peak field at min. gap: 1.181 T
:Effective field at min. gap: 1.114 T
:Effective K at min. gap: 1.66
:Photon energy range: 9.5 – 40 keV (operational use 15 – 35 keV)
:Electron beam size (RMS): 53.9 µm × 6.3 µm (h × v)
:Source size (RMS): 53.9 µm × 6.4 µm (h × v)
:Source divergence (RMS): 11.3 µrad × 9.8 µrad (h × v)
:Power at 4 mm gap: ~11 kW (total emitted)


Front End
---------

:Role: Mask + diagnostic stack that defines the maximum
   acceptance of the beamline and protects downstream optics.
:Family: BeamMask + Diagnostics
:Vendor: **Toyama** (essentially an upgraded version of the MAX IV
   phase-1 front ends)
:Layout:
   - Fixed mask 1: 1 × 1 mrad² aperture, absorbs ~70 W (wide
     opening to allow XBPM functionality).
   - **XBPM1** and **XBPM2** — two beam position monitors
     separated by ~3 m (z = 12.0 m and z = 15.1 m), feed an orbit
     correction loop.
   - Fixed mask 2: 100 × 100 µrad² aperture, absorbs up to 9.5 kW
     (z ≈ 16 m). Defines the maximum opening.
   - **Movable masks** downstream of FM2 — restrict acceptance to
     35 × 35 µrad² in normal operation, transmitting at most
     ~125 W. PLC-interlocked: the mask opening cannot exceed
     35 × 35 µrad² if the undulator gap is closed and storage-ring
     current is high.
:FE Aperture position: z ≈ 19.2 m
:Ratchet wall: z = 21.8 m (boundary into the optics hutch)


Optics hutch (z = 22.5–32.5 m)
------------------------------

All optical elements with their longitudinal positions (DDR
Table 3) — distance from the centre of the IVU16 straight:

===========================================  ===========  =================
Component                                    Length (mm)  Centre z (mm)
===========================================  ===========  =================
IVU16 (source)                               3700         0
XBPM 1                                       —            12000
XBPM 2                                       —            15100
FE Aperture                                  —            19200
Ratchet Wall                                 1400         21800
Trigger Unit                                 500          22750
Bremsstrahlung collimator                    200          23170
Diamond filter (high-pass)                   200          23520
Laue Monochromator (placeholder)             400          23890
White beam slit 1                            300          24460
BV1                                          300          24910
hDCM (Si 111)                                750          25655
BV2                                          300          26400
hMLM (multilayer)                            1000         27270
White beam stop + bremss. collimator         400          28190
Monochromatic slit 1                         100          28440
BV3 + future BPM                             500          30550
CRL transfocator                             600          31100
Monochromatic slit 2                         100          31600
Safety shutter                               400          32200
OH end wall                                  200          32500
===========================================  ===========  =================

High-pass diamond filter
~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Absorbs the low-energy fundamental harmonic (~2.2 keV)
   from the IVU16, dropping the heat load on every downstream
   optical element.
:Family: Filter
:Material: Single-crystal diamond, total thickness **1.0 mm**
   (stacked plates)
:Position: 23.52 m from source
:Power absorbed: ~75 W (transmits ~55 W at peak)
:Transmission: ~75 % at 15 keV, 92 % at 35 keV

hDCM — Horizontally-deflecting Double-Crystal Monochromator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: High-resolution monochromatic mode (ΔE/E ~ 10⁻⁴). Provides
   the cleanest beam for spectroscopy-grade imaging and diffraction.
:Family: Monochromator
:Geometry: Horizontally deflecting (consistent with BioMAX,
   NanoMAX, CoSAXS) — chosen for mechanical rigidity (no gravity
   on the rotation axis) and freedom from vertical-divergence
   broadening.
:Crystals: Si (111), two-bounce, fixed exit
:Cooling: Liquid nitrogen on both crystals (Si negative-thermal-
   expansion regime; produces a slight horizontal thermal-focus
   bump)
:Energy range: 15 – 35 keV
:Energy resolution (ΔE/E, FWHM): 1.7 × 10⁻⁴ @ 15 keV to
   3.2 × 10⁻⁴ @ 35 keV (< 1 × 10⁻⁴ ΔE/E RMS in design)
:Horizontal offset (fixed-exit mode): 10 mm (DCM alone),
   4 mm (when combined with the MLM downstream)
:Position: 25.655 m from source
:Power on first crystal: 71 W (peak power density < 20 W/mm²)
:Beam divergence (after hDCM):
   meridional 5–6 µrad RMS, sagittal 3–4 µrad RMS

hMLM — Horizontally-deflecting Multilayer Monochromator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: High-intensity quasi-monochromatic mode (ΔE/E ~ 0.3–1 %).
   Optimal for fast absorption-contrast imaging and fast
   diffraction; also used downstream of the hDCM for harmonic
   rejection at low energies (15–20 keV).
:Family: Monochromator
:Geometry: Horizontally deflecting, two-bounce, fixed exit
   (10 mm offset alone; 6 mm offset when combined with hDCM)
:Multilayer coatings: Two adjacent coatings on a single Si
   substrate per mirror:

   - **B₄C / W**, 25 Å period, Γ = 0.8, 200 bilayers
     (ΔE/E ~ 1 % bandwidth — main high-intensity coating)
   - **B₄C / Ni₀.₉₅V₀.₀₆** (Ni–V alloy), 20 Å period, Γ = 0.65
     (ΔE/E ~ 3.5 × 10⁻³ — intermediate-bandwidth option)

:Substrate roughness: < 3 Å RMS (interface) / ~1 Å RMS (substrate)
:Incident-angle range: 0.41° – 0.97°
:Cooling: 1st mirror water-cooled; 2nd mirror copper-braided or
   solid-Cu thermal link into cooled EGaIn bath
:Power absorbed (MLM-only mode): ~65 W on 1st mirror (~2.5 W/mm²)
:Position: 27.27 m from source

Operating modes (DDR §4):

- **hDCM only** — high-resolution monochromatic, no harmonic
  rejection; PXRD and phase-contrast imaging in the cleanest band.
- **hDCM + hMLM** — high-resolution mono with harmonic rejection
  (necessary at 15–20 keV where harmonic contamination is
  significant, λ₃/λ₁ ~ 5 × 10⁻⁴ from hDCM alone).
- **hMLM only** — quasi-monochromatic, maximum flux. Best for
  fast absorption-contrast imaging and fast (low-resolution)
  diffraction.

CRL transfocator
~~~~~~~~~~~~~~~~

:Role: First focusing element of the beamline. Be CRL pack
   delivers a stable, convenient spot-size variation at the
   sample.
:Family: CRL (compound refractive lens transfocator)
:Position: 31.1 m from source (in the optics hutch)
:Lens material: Beryllium
:Lens count: 50, grouped into 6 selectable sub-groups
:Adjustable spot size at sample: ~40 × 10 µm² to 1.3 × 1.2 mm²
   (FWHM, with hDCM); ~10 × 100 µm² to 1.2 × 1.3 mm² (with MLM)
:Beam divergence at sample (focused / collimated):

   - hDCM collimated: ~1.5 × 1 µrad² RMS (h × v)
   - hMLM collimated: ~5 × 0.5 µrad² RMS (h × v)

Diagnostics (BV1, BV2, BV3)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

:Role: Retractable beam viewers after each major optical element.
   Single physical design used at all three positions.
:Family: Diagnostics
:Mechanism: Diamond foil screen — Compton-scattering intensity →
   in-vacuum diode, fluorescence → optical microscope + GigE
   digital camera.
:Insertion: Pneumatic actuator (retractable; in operation at the
   cost of lower transmission downstream).
:Positions: BV1 at 24.91 m, BV2 at 26.40 m, BV3 at 30.55 m.


Radiation safety
----------------

:Role: Bremsstrahlung collimators + white-beam stops + safety
   shutter at the downstream end of the optics hutch protect EH1
   and personnel.
:Family: Shutter / Stop
:Layout:
   - Bremsstrahlung collimator at OH entrance (z = 23.17 m)
   - White-beam stop + bremsstrahlung collimator downstream of
     hMLM (z = 28.19 m)
   - Optional 3rd bremsstrahlung collimator upstream of photon
     shutter (TBD per DDR §7.7)
   - Photon (safety) shutter at z = 32.20 m


Experimental stations (EH1)
===========================

EH1 is a 13.8 m × 4.5 m hutch hosting three sample positions in
series along the beam. A common kinematic detector mount lets
(nearly) all detectors at DanMAX be used on any instrument.

Imaging instrument
------------------

:Role: Full-field imaging (absorption / phase / grating contrast)
   for *in situ* / *operando* studies of bulk materials and
   objects. Designed for 50 nm – 5 µm spatial resolution and
   time-resolved 3D acquisitions.
:Family: Imaging endstation
:Sample position: z ≈ 41.2 m from source
:Sample stage: Precise tomographic rotation stage on an
   **air bearing**, mounted on a **granite support** (which also
   carries the near-field detector and the EH-side focusing
   optics).
:Near-field detector: directly downstream of the sample, on the
   granite block (removable / movable inboard-outboard to clear
   the beam to the PXRD instruments).
:Far-field detectors: e.g. for diffraction-contrast imaging, can
   be positioned anywhere from 8 to 10 m downstream of the sample
   for grazing scattering-angle work, or at the far end of the
   hutch for ptychography.
:Beam shaping: A vacuum window + beam monitor at the EH1 upstream
   wall (z ≈ 38.1 m), followed by an in-hutch focusing device or
   devices that tightly focus the beam at z ≈ 41.2 m.

High-resolution PXRD instrument
-------------------------------

:Role: Medium-to-high resolution powder X-ray diffraction with a
   sample-changer robot for high-throughput.
:Family: PXRD endstation
:Sample position: z ≈ 44.8 m from source
:Diffractometer: Two-circle
:Detector: 1D strip detector, **Dectris Mythen 24K** (or similar)
:Sample handling: Robot sample changer
:Sample environments: Height-adjustable table in front of the
   goniometer for small-to-medium sample environments.

Large-sample-environment PXRD instrument
----------------------------------------

:Role: PXRD for heavy / large sample environments (e.g. magnets,
   pressure cells, gas rigs).
:Family: PXRD endstation
:Sample position: z ≈ 48.2 m from source
:Sample stage: **Hexapod**, carrying up to ~250 kg
:Detector: 2D area detector, **Dectris Pilatus 3X CdTe 2M**
   (or similar). Movable on a highly flexible XYZ mount, with
   adjustable 2θ.
:Additional detectors: Energy-dispersive detector option (for
   fluorescence signal); motorised beam stops with beam-imaging /
   intensity-recording capability.
:Detector-share: The 2D area detector mount is shared with the
   far-field imaging detector — same kinematic interface
   regardless of which instrument is in use.

Sample environments and gas system
----------------------------------

EH1 carries a permanent gas system (similar to the one at
BALDER) plus process ventilation with fume-extraction arms and
gas sensors for *in situ* gas / chemistry experiments. A
sample-environment (SE) preparation area sits adjacent to the
control room.


Control system
==============

DanMAX runs the standard MAX IV control stack — not EPICS:

:Backbone: `TANGO <http://www.tango-controls.org>`__ (process bus)
:User interface / sequencer: `Sardana <http://www.sardana-controls.org>`__
:Motor electronics: ESRF `IcePAP <http://www.esrf.eu/Instrumentation/DetectorsAndElectronics/icepap>`__

The user-facing control interfaces are Sardana macros + GUIs on
top of TANGO. The MAX IV Control & IT group provides the core
hardware-to-network plumbing; advanced data-collection and sample-
environment-integration layers are developed by the DanMAX team.

For the on-going APS ↔ DanMAX collaboration on the tomography
scan engine — mapping the existing APS 2-BM ``tomoscan`` onto
DanMAX's Sardana-based equivalent — see
:doc:`danmax_tomoscan_collaboration`.


References
==========

- **DanMAX Detailed Design Report** v2.0, December 2017,
  I. Kantor, E. Bergbäck Knudsen (DTU) and M. R. V. Jørgensen
  (Aarhus University) (``DanMAX_DDR_v2.pdf``, 73 pages).
- MAX IV — DanMAX beamline-optics page:
  https://www.maxiv.lu.se/beamlines-accelerators/beamlines/danmax/beamline-optics/
- Conceptual Design Report (Gundlach *et al.*, 2014).
- Project-overview abstract, ICTMS 2017:
  https://meetingorganizer.copernicus.org/ICTMS2017/ICTMS2017-122.pdf
- For TomoWISE (the second MAX IV imaging beamline, currently
  under design): :doc:`tomowise`.
