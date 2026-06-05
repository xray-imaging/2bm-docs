=====================
Max IV Collaboration
=====================

Background
==========

Max IV (DanMAX beamline) is designing a new tomography scan macro (``tomoscan``)
for their Sardana/Tango control system. They circulated a requirements document
describing the intended workflow and parameters, below is a detailed description
of how it maps onto the existing APS 2-BM ``tomoscan`` implementation
(`tomoscan-decarlof <https://github.com/decarlof/tomoscan>`_).

This page documents the comparison: first the architectural difference in how
scan-related variables are stored and propagated into the HDF5 file, then a
step-by-step and parameter-by-parameter match-up between the two
implementations.


Where scan parameters live (and how they reach the HDF5 file)
=============================================================

The single most important architectural difference between the two
implementations is **where each scan parameter lives at runtime and how it ends
up in the output HDF5 file** (the ``/process`` group at 2-BM, the dxchange
section at Max IV).


2-BM model: parameters as EPICS PVs → NDAttributes → HDF5 layout
----------------------------------------------------------------

At 2-BM there are four cooperating pieces, none of which is Python writing
HDF5:

1. **PVs are defined in EPICS database templates** (``.template``) and
   instantiated by the IOC at boot from a ``.substitutions`` file:

   * `tomoScanApp/Db/tomoScan.template <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/tomoScanApp/Db/tomoScan.template>`__,
     `tomoScan_2BM.template <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/tomoScanApp/Db/tomoScan_2BM.template>`__,
     `tomoScan_PSO.template <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/tomoScanApp/Db/tomoScan_PSO.template>`__,
     `tomoScan_Helical.template <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/tomoScanApp/Db/tomoScan_Helical.template>`__, …
   * Loaded by `iocBoot/iocTomoScan_2BMB/tomoScan.substitutions <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/iocBoot/iocTomoScan_2BMB/tomoScan.substitutions>`__
     + `st.cmd <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/iocBoot/iocTomoScan_2BMB/st.cmd>`__.
   * **Persistent across reboots via autosave**
     (`auto_settings.req <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/iocBoot/iocTomoScan_2BMB/auto_settings.req>`__,
     `save_restore.cmd <https://github.com/tomography/tomoscan/blob/2360976c035dbd1d2e0e376af1212f85f856632c/iocBoot/iocTomoScan_2BMB/save_restore.cmd>`__).
     User-edited scan parameters survive IOC restarts.
   * Every parameter is therefore a named, network-addressable record that any
     EPICS client (MEDM, pyepics, ``caput`` from a shell) can read or write at
     any time — not just ``tomoscan``.

2. **TomoScanDetectorAttributes.xml** tells the areaDetector camera plugin to
   attach a list of EPICS PV values as **NDAttributes** to every image array.
   Excerpt::

     <Attribute name="SampleX"     type="EPICS_PV" source="2bmb:m63.RBV"  ... />
     <Attribute name="RingCurrent" type="EPICS_PV" source="S:SRcurrentAI" ... />

   At runtime the camera IOC reads each ``source`` PV and tags each NDArray
   with ``name=value``. This file is selected at scan start via the
   ``CamNDAttributesFile`` PV (``tomoscan_2bm.py:58``).

3. **TomoScanLayout.xml** tells the **ADHdf5 file-writer plugin** how to build
   the HDF5 tree and which NDAttribute populates each dataset. The
   ``/process`` group seen in 2-BM output files is declared here::

     <group name="process">
       <group name="acquisition">
         <dataset name="scan_type"  source="ndattribute" ndattribute="ScanType"
                  when="OnFileOpen"/>
         <group name="sample">
           <dataset name="in_x" source="ndattribute" ndattribute="SampleInX"
                    when="OnFileClose"/>
           ...

   Selected at scan start via the ``FPXMLFileName`` PV
   (``tomoscan_2bm.py:59``). The same layout file also places image data under
   ``/exchange/data``, ``/exchange/data_white``, ``/exchange/data_dark`` (the
   DXchange convention).

4. **Python TomoScan2BM does not write HDF5 itself** (except the two add-ons
   ``add_theta()`` and ``web_camera_frame``, ``tomoscan_2bm.py:531–588``). It
   just sets PVs and starts the acquisition; the IOC's C++ writer plugin
   assembles ``/process``, ``/measurement``, ``/exchange``, etc. from the
   NDAttribute stream flowing through the plugin chain.

Consequences:

* Parameters are **persistent** (autosave) and **introspectable** (any EPICS
  client).
* A free MEDM GUI — every parameter is already a PV.
* Adding a new field in the HDF5 = add an ``<Attribute>`` to the attributes
  XML + a ``<dataset>`` to the layout XML. **No Python change required.**
* HDF5 writing happens in IOC C++ at line-rate; Python is out of the per-frame
  loop.
* Schema is fragmented across two XML files that must stay in sync (each
  parameter must be declared as an NDAttribute AND have a layout entry).


Max IV model: macro args + Tango attributes → JSON-driven Python writer
-----------------------------------------------------------------------

From the Max IV requirements document, parameters live in three different
places, none of them a persistent named record per parameter:

1. **Most scan parameters are macro arguments** to the ``tomoscan`` Sardana
   call: ``scan_type``, ``rot_mot``, ``scan_start``, ``scan_end``, ``nsteps``,
   ``exp_time``, ``latency``, ``npt_fields``, ``tomo_det``, ``posflat``,
   ``condition``, ``tori_name``, ``center``, plus the binary ``fields_flag``
   string. They exist only for the duration of the macro call — per-scan, no
   autosave, no IOC.

2. **Some parameters live as Tango device attributes** on the
   ``tomo/configurator`` device — the document explicitly mentions ``DarkFile``
   and ``WhiteFile`` being updated on it (steps 2.4, 3.8). The detector device
   carries a ``tomo_det.label`` Tango attribute that the macro sets to
   ``"dark"``, ``"flat"``, ``"proj"``, ``""`` to label each phase (steps 2.2,
   2.5, 3.5, 3.11, 4.1, …).

3. **Environment context** comes from Sardana env vars — e.g.
   ``self.getEnv('ScanDir')`` (used in step 3.1 to locate the visit folder and
   ``.positions/`` directory).

The HDF5 itself is built by **Python code** (the ``make_dxchange_file()``
function in ``danmax_tomo.py``) driven by a **dxchange_configuration.json**
mapping file. The JSON describes each output dataset as one of four kinds:

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Entry type
     - Source of value
   * - ``h5 link``
     - Symbolic link to a dataset (or slice) in another HDF5 file
   * - ``h5 copy``
     - Copy of a dataset (or slice) in another HDF5 file
   * - ``tango``
     - Read from a Tango attribute at write-time
   * - ``value``
     - Literal value provided in the JSON

So the analog of 2-BM's
``<Attribute … type="EPICS_PV" source="…">`` is the JSON ``tango`` entry; the
analog of layout's
``<dataset name="in_x" ndattribute="SampleInX"/>`` is a JSON entry mapping
``process/acquisition/sample/in_x`` ← ``tango://…/SampleInX``.


Side-by-side: where scan parameters live and how they reach HDF5
----------------------------------------------------------------

.. list-table::
   :header-rows: 1
   :widths: 25 38 37

   * - Concern
     - 2-BM (EPICS)
     - Max IV (Sardana/Tango)
   * - Where a scan parameter "lives"
     - Persistent EPICS PV in the tomoscan IOC
     - Macro argument (per-call) + selected Tango device attributes
   * - Persistence across reboots
     - Autosave restores all PVs
     - None inherent — user re-types args (or wraps in a Sardana
       macro/script that hard-codes them)
   * - External readability
     - Any EPICS client at any time
     - Only callers that know the Tango device + attribute, or readers of
       ``scan-####.h5`` after the fact
   * - GUI
     - Free MEDM screen, one widget per PV
     - Sardana CLI/GUI per macro; ``tomo/configurator`` widgets need to be
       authored
   * - HDF5 writer
     - areaDetector ADHdf5 plugin (C++, in IOC) — Python is not in the
       per-frame path
     - Pure Python (``make_dxchange_file``) called by the macro
   * - Mapping PV/attribute → HDF5 path
     - Two XML files (``*DetectorAttributes.xml`` + ``*Layout.xml``)
     - One JSON file (``dxchange_configuration.json``)
   * - Entry types supported
     - NDAttribute (EPICS_PV, EPICS_PV.timestamp, PARAM, …), constant value,
       dataset from frames
     - ``h5 link``, ``h5 copy``, ``tango``, ``value``
   * - When values are sampled
     - Per ``when`` attr — ``OnFileOpen`` / ``OnFileClose`` / per-frame
     - At HDF5 write time (after scan) — ``OnFileClose`` equivalent only
   * - Adding a new metadata field
     - Add ``<Attribute>`` to attributes XML + ``<dataset>`` to layout XML;
       no Python change
     - Add an entry to the JSON config; Python ``make_dxchange_file()``
       consumes it generically
   * - Where DXchange image data comes from
     - Written by the file plugin in real time from the NDArray stream,
       into ``/exchange/data*``
     - Linked (``h5 link``) or copied (``h5 copy``) from the standard
       Sardana ``scan-####.h5`` into a dxchange section/file after the scan
   * - Number of HDF5 files per scan
     - One (DXchange groups live inside the single ADHdf5 file)
     - Open in the spec — either the Sardana master ``scan-####.h5`` with
       extra dxchange groups, **or** an extra ``scan-####_dxchange.h5``
       (DanMAX's current behavior)
   * - ``/process/acquisition`` analog
     - Built automatically by the layout XML group of the same name
     - Built by JSON entries pulling from Tango attributes / values

**Architectural takeaway.** At 2-BM, the IOC is the source of truth for scan
parameters and the file plugin is the writer; Python is a thin orchestrator.
At Max IV, the macro call is the source of truth for scan parameters and a
Python function with a JSON spec is the writer; persistent state is limited to
whatever the configurator Tango device chooses to expose.

The trade-off is:

* 2-BM gets persistence, free GUIs, external scriptability of every parameter,
  and Python out of the per-frame data path — at the cost of an EPICS IOC +
  autosave + two XML files to keep in sync.
* Max IV gets simpler deployment (no IOC), config in one JSON, and the
  flexibility of ``h5 link``/``copy`` to compose dxchange from arbitrary
  Sardana scan files — at the cost of no parameter persistence, no native GUI,
  and Python in the metadata-writing path.

If Max IV wants the 2-BM-style "every scan parameter is its own named,
persistent, externally-visible record," the equivalent move in their stack
would be to promote each scan parameter to a memorized attribute on
``tomo/configurator`` rather than leaving them as transient macro arguments.


Scan-sequence step-by-step comparison
=====================================

The Max IV requirements document lists 11 ordered steps for the scan macro.
The table below maps each step to the matching 2-BM behaviour and points to
the relevant source location.

.. list-table::
   :header-rows: 1
   :widths: 4 32 32 32

   * - #
     - Max IV requirement
     - 2-BM status
     - Where in 2-BM code / notes
   * - 1
     - **Arm area + scalar detector ONCE for all frames** (total =
       dark + flat + proj + ortho + flat_after)
     - **Partial mismatch.** File plugin is armed once for the total frame
       count, but the area detector itself is re-armed per phase (Internal
       mode for dark/flat, PSOExternal for projections).
     - ``begin_scan()`` sets ``FPNumCapture = total_images``
       (``tomoscan_pso.py:106``); ``set_trigger_mode()`` re-arms the camera
       each phase (``tomoscan_2bm.py:354``).
   * - 2
     - Dark before (optional)
     - Match.
     - ``DarkFieldMode ∈ {None, Start, End, Both}``, ``collect_dark_fields()``
       (``tomoscan.py:753``).
   * - 3
     - Flat before (optional)
     - Match (but no ``posflat`` file — see parameters table below).
     - ``FlatFieldMode``, ``collect_flat_fields()`` (``tomoscan.py:776``).
   * - 4
     - Sample angular scan, branching on ``scan_type``
       (ascan / ascanct / dscan / dscanct / timescan)
     - **Partial.** 2-BM supports Standard (fly), Helical, Step, FPGA, Stream
       variants — but as separate Python classes, not a single ``scan_type``
       enum. No dscan/dscanct/timescan Sardana-style modes.
     - ``tomoscan_2bm.py`` (fly), ``tomoscan_2bm_step.py`` (step),
       ``tomoscan_fpga_2bm.py`` (FPGA), ``tomoscan_stream_2bm.py`` (stream).
   * - 5
     - **Collect orthogonal angles on rotation back (every 90°)**
     - **MISSING.**
     - No feature in any 2-BM variant. Return-to-start just rotates back.
   * - 6
     - Flat after (optional)
     - Match.
     - ``FlatFieldMode = End/Both``.
   * - 7
     - Close fast shutter
     - Match.
     - ``close_shutter()`` (``tomoscan_2bm.py:338``).
   * - 8
     - Dark after (optional)
     - Match.
     - ``DarkFieldMode = End/Both``.
   * - 9
     - **Create dxchange file**
     - Match (different mechanism).
     - 2-BM writes DXchange-compliant HDF5 directly via the AreaDetector
       ADHdf5 plugin using ``TomoScanLayout.xml`` — one file, no separate
       ``_dxchange.h5``. ``theta`` added post-scan in ``add_theta()``
       (``tomoscan_2bm.py:588``).
   * - 10
     - Rotate back to start (optional)
     - Match.
     - ``ReturnRotation ∈ {Yes, No, Home}``, with mod-360 wrap
       (``tomoscan_2bm.py:511–521``).
   * - 11
     - **Initiate reconstruction** (tori file, auto COR, etc.)
     - **No built-in recon trigger.** Closest analog: post-scan FDT/SCP data
       transfer to analysis machine (``CopyToAnalysisDir``,
       ``tomoscan_2bm.py:558–572``).
     - No ``tori``-style config; no COR-from-file/auto switch.


Scan-parameter comparison
=========================

.. list-table::
   :header-rows: 1
   :widths: 22 38 40

   * - Max IV parameter
     - 2-BM equivalent
     - Notes
   * - ``fields_flag`` (binary string of 6 bits → booleans)
     - Not used. Individual EPICS PVs/enums (``DarkFieldMode``,
       ``FlatFieldMode``, ``ReturnRotation``, …).
     - Max IV's "binary key" UX has no 2-BM analog.
   * - ``get_dark`` / ``get_dark_after``
     - ``DarkFieldMode`` (Start/End/Both/None)
     - Combined into one enum.
   * - ``get_flat`` / ``get_flat_after``
     - ``FlatFieldMode`` (Start/End/Both/None)
     - Combined into one enum.
   * - ``get_ortho``
     - **Missing.**
     - No orthogonal-back-projection feature.
   * - ``return_to_start``
     - ``ReturnRotation``
     -
   * - ``npt_fields`` (# of dark/flat frames)
     - ``NumDarkFields``, ``NumFlatFields``
     - Separated.
   * - ``scan_type`` (ascan/ascanct/dscan/dscanct/timescan)
     - Partial — encoded as class choice, not parameter.
     - ``ScanType`` PV only toggles Standard ↔ Helical.
   * - ``rot_mot``
     - ``Rotation`` motor PV
     - Hard-coded per beamline, not a runtime arg.
   * - ``scan_start`` / ``scan_end`` / ``nsteps``
     - ``RotationStart``, ``RotationStep``, ``NumAngles``
     -
   * - ``exp_time``
     - ``ExposureTime`` (+ optional ``FlatExposureTime`` via
       ``DifferentFlatExposure``)
     - 2-BM **extra**: separate flat exposure time.
   * - ``latency``
     - Implicit (camera/PSO timing in ``compute_frame_time()``)
     - No user-facing latency parameter.
   * - ``tomo_det`` (select 2D detector)
     - **Two mechanisms.** (1) Runtime swap between two pre-wired cameras via
       the ``MctOptics`` IOC's ``CameraSelect`` PV (Optique Peter dual-camera
       optics box): a PV callback (``pv_callback_2bm`` → ``reinit_camera``,
       ``tomoscan_2bm.py:109–134``) reconnects ``Camera0/1``,
       ``FilePlugin0/1``, ``PvaPlugin1``, ``RoiPlugin0/1``, ``CbPlugin0/1`` PV
       prefixes on the fly — no IOC reboot, no tomoscan restart. (2) Adding
       or swapping to a different camera model (e.g. Oryx → Grasshopper, or
       adding a third camera) requires editing the IOC's ``CameraXPVPrefix``
       configuration and rebooting the tomoscan IOC so it rebinds to the new
       areaDetector prefix. Only one camera is acquiring at a time; there is
       no concurrent multi-detector acquisition or per-detector dxchange
       routing.
     - Open question in the Max IV spec: auto-detect which detector(s) in the
       Sardana measurement group are 2D, and potentially write one dxchange
       file per active 2D detector (concurrent multi-detector).
   * - ``posflat`` (named flat-field position loaded from
       ``.positions/<name>`` file, can list any motors)
     - **Partial match.** 2-BM has a dedicated "move sample out for flats"
       mechanism via ``SampleOutX``, ``SampleOutY``, ``FlatFieldAxis``, plus
       optional ``SampleOutAngleEnable`` + ``SampleOutAngle`` to rotate the
       sample first — directly covers the **odd-shape-sample** use case
       (rotate edge-on, then translate out). On return, the pre-flat angle is
       restored from ``self.rotation_save`` (``tomoscan.py:443–450,
       463–483``).
     - **Missing vs. Max IV:** (a) only X, Y, and rotation are supported — no
       way to script motion of arbitrary auxiliary motors (e.g. ``tom_sam_x``,
       ``tom_ry``, …); (b) only one "out" preset, not a named library of
       positions; (c) configured via EPICS PVs, not a file-per-position in
       the visit folder.
   * - ``condition`` (scintillator warm-up wait after shutter opens)
     - Hardcoded 2 s sleep after ``open_shutter()`` / ``close_shutter()``
       (``tomoscan_2bm.py:313, 351``).
     - Not user-tunable.
   * - ``tori_name``, ``center`` (auto/tori for COR)
     - Missing.
     - No reconstruction-config concept.


Infrastructure / cross-cutting comparison
=========================================

.. list-table::
   :header-rows: 1
   :widths: 22 40 38

   * - Topic
     - 2-BM
     - Max IV requirement
   * - Trigger HW
     - PSO (Aerotech) or FPGA (softGlueZynq)
     - PandABox
   * - Shutter model
     - Two shutters: front-end (slow) + fast
     - One fast shutter
   * - Detector framework
     - EPICS areaDetector + ADHdf5 file plugin
     - Tango (``tomo_det``, ``tomo/configurator``); explicit
       ``tomo_det.label`` set to ``"dark"`` / ``"flat"`` / ``"proj"`` / ``""``
       per phase
   * - Frame labeling per phase
     - Implicit via ``HDF5Location`` dataset path
     - Explicit ``tomo_det.label`` Tango attribute
   * - File format
     - Single HDF5 (DXchange in ``/exchange/...``) + JSON sidecar ``.config``
     - Open question — extra ``_dxchange.h5`` file OR DXchange entries in
       master file
   * - DXchange entry generator
     - XML layout drives the AreaDetector writer + ``add_theta()`` post-step
     - JSON-driven (``dxchange_configuration.json``) with ``h5 link``,
       ``h5 copy``, ``tango``, ``value`` entry types
   * - Multi-detector support
     - One acquiring at a time (Optique Peter dual-camera swap is runtime; a
       new camera model requires an IOC reboot)
     - Open question (potentially one dxchange file per 2D det, concurrent)
   * - Web-camera snapshot into HDF5
     - Yes (``/exchange/web_camera_frame``,
       ``tomoscan_2bm.py:531–555``)
     - Not mentioned
   * - Post-scan data transfer
     - FDT/SCP to analysis host
     - Not mentioned (covered later by ReconBlazer)
   * - Streaming reconstruction hooks
     - ``tomoscan_stream_2bm.py`` (PVAccess angle broadcast, circular buffer)
     - Not in initial scope


Bottom line
===========

**Matched well:** dark/flat before/after, return to start, single-HDF5 output
with DXchange-style groups, optional features toggled per-scan, helical/step
variants.

**Missing in 2-BM (must build for Max IV):**

1. Orthogonal projections on the way back to start.
2. Multi-motor / named-library flat-field positions. 2-BM already handles
   odd-shaped samples via ``SampleOutAngleEnable`` + ``SampleOutAngle`` +
   ``SampleOutX/Y``, but only with a fixed set of motors and a single preset.
   Max IV needs (a) arbitrary motor list per position, (b) a named library of
   positions stored as files under ``.positions/``, and (c) validation that
   the named position exists before scan start.
3. Reconstruction trigger with ``tori`` / ``center`` config.
4. ``scan_type`` as a runtime parameter unifying ascan / ascanct / dscan /
   dscanct / timescan.
5. User-tunable scintillator ``condition`` delay and ``latency`` parameter.
6. ``fields_flag`` binary-string UX.
7. Concurrent multi-detector acquisition with per-detector dxchange routing.

**2-BM features beyond the Max IV spec (worth porting):**

1. **Flat fields at a sample-specific angular position** —
   ``SampleOutAngleEnable`` + ``SampleOutAngle`` rotate the sample to a chosen
   angle before translating it out (``SampleOutX``, ``SampleOutY``,
   ``FlatFieldAxis``) and restore the pre-flat angle on return
   (``tomoscan.py:443–450, 463–483``). Critical for **odd-shape samples**
   where translating out at an arbitrary angle would otherwise clip the
   sample, the holder, or the field of view.
2. **Different exposure times for projections vs. flat fields** —
   ``DifferentFlatExposure`` toggles a separate ``FlatExposureTime`` that
   overrides ``ExposureTime`` during flat-field collection. Critical for
   **large / local tomography of very dense samples**, where projections need
   a long exposure to penetrate the sample but flats (taken with no sample in
   the beam) would saturate the detector at the same exposure and must be
   shortened.

**Architectural mismatches Max IV will have to decide:**

* Tango ``tomo_det.label`` vs. EPICS ``HDF5Location`` for frame labeling.
* Whether to arm the *camera* (not just the file writer) once for all frames
  — 2-BM only does this for the file plugin.
