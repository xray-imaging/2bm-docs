==========
Procedures
==========

Structured, cora-consumable recipes for operations on 2-BM hardware.

Each page in this section documents one procedure end-to-end:
preconditions, typed parameters, atomic steps, postconditions, and
known failure modes. The structure is deliberately rigid so that
every page maps cleanly onto a cora ``Method`` definition (see
`cora <https://github.com/xmap/cora>`__: ``Method.parameters_schema``,
``Plan.wiring``, the ``Procedure`` BC).

For the human-narrative walkthrough — which buttons to push on which
MEDM, how to read the screen, when to call the floor coordinator —
keep using the :doc:`ops` pages. A procedure page may *link* to its
ops counterpart but should not duplicate it.

For the underlying hardware (PV names, intrinsic specs, kinematic
chain), see :doc:`manual/item_020`.


Current procedures
==================

- :doc:`procedures/item_001` — ``Procedure template``. Canonical
  skeleton for new procedure pages (Name / Devices / Preconditions /
  Parameters / Steps / Postconditions / Failure modes). Copy this
  file as ``item_NNN.rst`` to start a new procedure.
- :doc:`procedures/item_002` —
  ``detector_z_rail_alignment``. Walks the Optique Peter detector
  along its 1 m PRO225SL Z rail with a small square X-ray aperture
  defined by the B-station slits, fits the centroid drift, and uses
  the detector optical table (``2bmb:table3.AX`` / ``.AY``) to
  rotate the rail back parallel to the beam. Open trigger for a
  ``DetectorTable`` Asset (cora ``Table`` Family) on the cora
  side.
- :doc:`procedures/item_011` —
  ``centre_and_close_slits``. Drives an L3-style slit
  (``--slit-station A`` or ``B``) so the beam image sits at the
  centre of the detector frame, then incrementally closes
  ``Hsize`` / ``Vsize`` to a target (default 0 = fully closed).
  Two-phase: calibrate + iterate centring on
  ``Hcenter`` / ``Vcenter`` first, then sequential H / V size
  reduction. Operator typically follows with manual rezeroing
  of the four virtual motors to define a new origin. Open
  trigger for a per-station ``Slit`` Asset on the cora side.
- :doc:`procedures/item_012` —
  ``calibrate_slit_blade_throw``. Diagnostic procedure that
  drives each of a station's four blade motors by ``±blade_throw_mm``
  from baseline, measures the bbox edge shift of the bright spot
  on the detector, and reports per-blade ``pixels-per-mm`` slopes.
  Flags same-axis spread and V/H mean-ratio mismatches to
  identify mis-calibrated blade motors. No deliberate output --
  all blades restored to baseline. Open trigger to extend the
  per-station ``Slit`` Asset with per-blade calibration fields
  on the cora side.
- :doc:`procedures/item_013` —
  ``nv200_trigger_step``. Loads the per-axis coded-aperture
  position list (default 256, max 1024 positions per axis) into
  both Piezosystem Jena NV200D/NET controllers via Telnet and
  arms them so each rising edge on TRG IN advances to the next
  position in the buffer. ``--random`` selects compressive-sensing
  dithered sampling (the current operational mode); ``--linspace``
  (default) gives an evenly-spaced raster. Run once at session
  setup or whenever the random list needs regenerating;
  controllers then drive themselves frame-by-frame from the FPGA
  trigger for the rest of the session. Targets the two
  ``CodedApertureFineDrive_X`` / ``_Y`` Assets and the coded-
  aperture XY flexure stage on the cora side.


Stub procedures
===============

These pages exist so the precondition graph of
:doc:`procedures/item_002` has named targets to link to. Each is a
STUB — the procedure is named, its Postconditions field declares
which state it would satisfy, and the rest is TBD until the
procedure is implemented.

- :doc:`procedures/item_003` — ``enable_beamline``. Satisfies
  ``beamline_enabled``.
- :doc:`procedures/item_004` — ``set_a_slits``. Satisfies
  ``a_slits_open``.
- :doc:`procedures/item_005` — ``set_energy_to_preselect``.
  Satisfies ``energy_configured``. Two-path procedure: exact-match
  pre-calibrated energy uses the ``energy2bm.json`` ``store_0`` row
  directly; off-table energy uses linear interpolation between the
  two bracketing calibrated energies. Both paths are already live
  in the deployed ``decarlof/energy`` fork (a fork of
  ``xray-imaging/energy`` reconciled periodically via PR;
  deployment lives at ``/home/beams/2BMB/epics/synApps/support/energy/``).
  The formal 2bm-procedures wrapper is pending; the operator will
  validate the linear-interp assumption per motor when writing it.
- :doc:`procedures/item_006` — ``set_flag_in``. Satisfies
  ``flag_in_beam``. (Flag is ``2bma:m44`` in
  :doc:`manual/item_020`; energy-dependent target Y from
  `energy2bm.json
  <https://github.com/decarlof/energy/blob/main/src/energy/data/energy2bm.json>`__.)
- :doc:`procedures/item_007` — ``open_b_shutter``. Satisfies
  ``b_shutter_open``.
- :doc:`procedures/item_008` — ``set_b_slits``. Satisfies
  ``b_slits_configured``.
- :doc:`procedures/item_009` — ``move_sample_out_of_beam``.
  Satisfies ``sample_out_of_beam``.
- :doc:`procedures/item_010` —
  ``configure_microscope_for_alignment``. Satisfies
  ``microscope_configured``.


.. toctree::
   :glob:
   :maxdepth: 1
   :hidden:

   procedures/item*
