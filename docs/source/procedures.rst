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

.. toctree::
   :glob:
   :maxdepth: 1

   procedures/item*
