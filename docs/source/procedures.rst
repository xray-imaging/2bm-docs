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
  rotate the rail back parallel to the beam. Open trigger for an
  ``OpticalTable`` Family + ``Detector_optical_table`` Asset on the
  cora side.


.. toctree::
   :glob:
   :maxdepth: 1
   :hidden:

   procedures/item*
