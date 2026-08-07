=======================================
Run the no-DB test lane (``test-noio``)
=======================================

Purpose
=======

Confirm the CORA install is complete and correct by running every
test that does not touch a database. This exercises the pure-logic
tier (deciders, evolvers, invariants), the architecture fitness
functions (structural rules about the codebase), and the surface
contracts (REST + MCP schemas backed by the in-memory kernel). No
Docker, no Postgres, no network egress.

The suite is large (about 47,000 tests) and takes roughly half an
hour on a typical 4-worker box. This is a one-time confidence check,
not something you would run on every change; for day-to-day work
you would run only the tests that touch your slice.


Prerequisites
=============

- :doc:`item_001` completed successfully.
- The ``cora`` conda env activated.
- No CORA server needs to be running — the test lane spins up its
  own in-memory app.


Steps
=====

1. From the CORA **repository root** (not from ``apps/api/``; the
   Makefile lives at the top of the tree and every target starts
   with ``cd apps/api && ...`` under the hood):

   .. code-block:: bash

      conda activate cora
      cd /home/beams0/2BMB/conda/cora-decarlof
      make test-noio

2. Watch the progress. In pytest's short-form output:

   - ``.`` — passed
   - ``s`` — skipped (expected: many groups of ``s`` in a row are
     modules whose whole file is skipped because a required
     optional dependency, platform, or DB backend is not present)
   - ``F`` or ``E`` — failed / errored (should not appear on a
     healthy install)

3. Wait for the final summary line, which looks like:

   .. code-block:: text

      46550 passed, 622 skipped, 1 warning in 1796.47s (0:29:56)

   The single warning is BoTorch's torch backend reporting that the
   NVIDIA driver on the workstation is older than the one PyTorch
   was compiled against. Harmless — torch falls back to CPU.


Verify
======

If the summary shows ``passed`` on the left and no ``failed`` /
``errors``, the install is healthy. Any ``F`` or ``E`` should be
copied verbatim and diagnosed before proceeding to :doc:`item_003`.


Notes
=====

- **Speeding up the run.** The Makefile hardcodes ``-n 4`` pytest
  workers. If your workstation has more cores, override the parallel
  count directly:

  .. code-block:: bash

     cd apps/api
     uv run pytest -n 8 --dist=worksteal tests/unit tests/architecture tests/contract

- **Fast smoke check.** For a subset that finishes in about a
  minute, run only the unit tier:

  .. code-block:: bash

     cd apps/api
     uv run pytest -n 8 -m unit

- **What the noio lane does not cover.** ``tests/integration`` and
  ``tests/e2e`` run the same handlers against a real Postgres
  cluster and are skipped here. If you later install a local
  Postgres (see the note at the end of :doc:`item_003`), you can
  run them with ``make test-db``.
