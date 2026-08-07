====
CORA
====

`CORA <https://github.com/xmap/cora>`__ (**C**\ ontinuously
**O**\ verpromised, **R**\ arely **A**\ utomated) is the system of
record for the experiment: the one place that holds why each run did
what it did, who or what approved it, under which recipe, and how to
replay it later. It owns no servo loop, runs no reconstruction, and
stores no dataset bytes; it records what our existing tools decide
and, where 2-BM wants it, governs the choices between them.

This section documents how to bring up a local CORA instance on a
2-BM workstation for exploration and development. The upstream README
assumes a laptop with Docker and unrestricted network access, neither
of which is true for the beamline network. Every page below explains
what the upstream instruction does and the beamline-specific
adaptation we use in its place.


Prerequisites
=============

- Access to two hosts that share ``$HOME`` over the beamline NFS
  mount:

  - **tocai** (public network) — used to run ``pip install`` for
    Python packages, since PyPI is reachable from here.
  - **arcturus** (private network) — where CORA actually runs. No
    outbound internet, so no ``pip install`` and no browser fetches
    from public CDNs.

  Because ``$HOME`` is shared, any conda env or Python package
  installed on tocai is visible on arcturus without a second install
  step.

- A clone of the CORA repository. The 2-BM install works from a
  personal fork of the upstream ``xmap/cora`` repository:

  - **Upstream**: https://github.com/xmap/cora
  - **2-BM fork**: https://github.com/decarlof/cora
  - **Local clone**: ``/home/beams0/2BMB/conda/cora-decarlof``

  Working from a fork gives 2-BM a two-way contribution loop:

  - **Local fixes and beamline-specific tweaks** land first on a
    branch of the fork (``decarlof/cora``), where they can be
    tested against the 2-BM install without waiting on upstream
    review.
  - **Anything worth sharing upstream** is then opened as a Pull
    Request from the fork into ``xmap/cora``. The fork acts as
    the staging ground; upstream stays clean until a change is
    ready.
  - **Periodic sync** with upstream happens by fetching
    ``xmap/cora`` (added as the ``upstream`` remote in the clone
    step) and rebasing the fork's ``main`` on top.

  :doc:`cora/item_001` documents the fork-and-clone setup step by
  step, including the two-remote configuration. Substitute your
  own clone path in every command below if you cloned elsewhere.

- No Docker required for the workflow documented here. The upstream
  ``make db-up`` starts Postgres in a container; we use CORA's
  in-memory kernel (``APP_ENV=test``) instead, which needs no
  container runtime and no schema migrations.


Steps in order
==============

Follow the pages below in sequence. Each one builds on the previous
and ends with a concrete verification step.

- :doc:`cora/item_001` — Create a dedicated ``cora`` conda env and
  install every Python dependency with ``uv`` inside it. Run once
  per machine (or once total if ``$HOME`` is shared).
- :doc:`cora/item_002` — Run the no-DB test lane (``make test-noio``)
  to confirm the install works end to end. About 30 min on a typical
  4-worker box; 46,000+ tests pass, hundreds skip.
- :doc:`cora/item_003` — Boot the FastAPI dev server against the
  in-memory kernel (no Docker, no Postgres) and smoke-test the REST
  surface with ``curl``.
- :doc:`cora/item_004` — Work around the private-network block on
  Swagger UI's CDN assets by serving the vendored Swagger UI bundle
  from a local static file server. Includes a screenshot of the
  rendered UI so you know what a successful bring-up looks like.
- :doc:`cora/item_005` — Daily restart recipe: two terminals, one
  ``conda activate cora`` in each, then the CORA server in one and
  the Swagger UI static server in the other. Also documents how
  to SSH into arcturus and resume the Claude Code session that
  drove this bring-up. Reference card for everyday use once the
  first four items have been done once.


.. toctree::
   :glob:
   :maxdepth: 1
   :hidden:

   cora/item*
