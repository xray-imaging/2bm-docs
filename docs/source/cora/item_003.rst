=====================================
Boot the dev server against in-memory
=====================================

Purpose
=======

Bring the CORA REST + MCP API up on ``localhost:8000`` without
Docker, Postgres, or Atlas. The trick is CORA's ``APP_ENV=test``
switch: it selects an in-memory kernel that skips the asyncpg pool
entirely and wires in-memory adapters for the event store,
idempotency store, and every port that would otherwise talk to
Postgres. Every command, every REST route, and every MCP tool works
against this kernel; the only trade-off is that state lives in
process memory and is lost on restart.

The upstream ``make dev`` target cannot be used here because it
depends on ``make db-up``, which starts a Postgres container.
Instead we invoke ``uvicorn`` directly with ``APP_ENV=test`` in the
environment.


Prerequisites
=============

- :doc:`item_001` completed.
- Recommended but not required: :doc:`item_002` passed at least
  once, so you know the install is healthy.


Steps
=====

1. In one terminal, activate the env and launch ``uvicorn`` with
   ``APP_ENV=test``:

   .. code-block:: bash

      conda activate cora
      cd /home/beams0/2BMB/conda/cora-decarlof/apps/api
      APP_ENV=test uv run uvicorn cora.api.main:app --reload --host 0.0.0.0 --port 8000

   The server logs a few structured startup lines and finishes with
   ``Uvicorn running on http://0.0.0.0:8000``. Leave this terminal
   open for the lifetime of the session.

2. In a second terminal, hit the liveness probe to confirm the
   process is answering:

   .. code-block:: bash

      curl http://localhost:8000/health
      # {"status":"ok","version":"0.1.0"}

3. Register a new Actor (a real command that flows through the
   whole handler → event store → response chain):

   .. code-block:: bash

      curl -X POST http://localhost:8000/actors \
        -H 'Content-Type: application/json' \
        -d '{"name": "Doga"}'
      # {"actor_id":"019fdaa0-e800-71c0-adfe-a5af26b1cc63"}

   The ``actor_id`` is a UUID7: its leading ``019f...`` bytes encode
   the creation timestamp, so IDs sort in commit order — that is why
   CORA chose UUID7 over UUID4 for aggregate identifiers.


Verify
======

- ``curl http://localhost:8000/health`` returns HTTP 200 with the
  ``status: ok`` payload.
- ``POST /actors`` returns HTTP 201 with a fresh ``actor_id``.
- The full OpenAPI schema is served at
  ``http://localhost:8000/openapi.json`` (about 1.2 MB, covers
  every command across all 17 bounded contexts).


Notes
=====

- **Interactive docs pages.** FastAPI mounts Swagger UI at
  ``/docs`` and ReDoc at ``/redoc``, but both fail to render on
  the beamline private network because their JavaScript is fetched
  from a public CDN. See :doc:`item_004` for the local workaround.
- **State loss on restart.** The in-memory kernel keeps the event
  log in a Python list. Every Actor, Recipe, Run, and event you
  create disappears when ``uvicorn`` exits or reloads. Fine for
  exploration; not fine for real event-sourcing work.
- **Later: adding Postgres without Docker.** If you eventually
  need durability or want to exercise the ``integration`` / ``e2e``
  test lanes, install Postgres directly into the conda env instead
  of running the upstream Docker container:

  .. code-block:: bash

     conda install -c conda-forge postgresql pgvector
     initdb -D ~/.local/share/cora-pg
     pg_ctl -D ~/.local/share/cora-pg -l ~/.local/share/cora-pg.log start
     createuser -s cora
     createdb -O cora cora

  Then install Atlas separately and run ``make migrate-apply`` from
  the CORA repo root to apply the schema. This is out of scope for
  the current 2-BM bring-up.
