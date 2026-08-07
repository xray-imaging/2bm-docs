============================
Restart CORA from scratch
============================

Purpose
=======

Once :doc:`item_001` through :doc:`item_004` have been done once, a
daily restart of the CORA server + Swagger UI is a two-terminal
recipe. Nothing needs to be re-installed, no schema needs to be
re-snapshotted (unless the CORA schema itself changed), and no
Docker or Postgres is involved.


Prerequisites
=============

- :doc:`item_001` through :doc:`item_004` all completed at least
  once, so the ``cora`` conda env and
  ``~/claude/cora/swagger-ui/`` serving directory both exist.


Steps
=====

Open two terminals on arcturus and activate the ``cora`` conda env
in each one.

**Terminal 1 — CORA server** (in-memory kernel, no Postgres):

.. code-block:: bash

   conda activate cora
   cd ~/conda/cora-decarlof/apps/api
   APP_ENV=test uv run uvicorn cora.api.main:app --reload --host 0.0.0.0 --port 8000

**Terminal 2 — Swagger UI static server:**

Using the helper script :doc:`item_004` installed:

.. code-block:: bash

   conda activate cora
   ~/claude/cora/swagger-ui/serve.sh

Or, equivalently, running the two commands that ``serve.sh`` wraps
directly (useful if you want to change the port on the fly or paste
into a fresh terminal without remembering the script path):

.. code-block:: bash

   conda activate cora
   cd ~/claude/cora/swagger-ui
   python -m http.server 8080 --bind 127.0.0.1

**Browser:** open ``http://localhost:8080/``. You should see the
CORA Swagger UI render as in :doc:`item_004`.


Verify
======

- Terminal 1 logs ``Uvicorn running on http://0.0.0.0:8000``.
- Terminal 2 logs ``Serving HTTP on 127.0.0.1 port 8080``.
- Browser shows the CORA title bar with a green ``OAS3`` pill and
  the ``access`` / ``trust`` / other BC groups.


Resume the Claude Code session
==============================

The Claude Code conversation that built this section (and that you
can hand new CORA tasks off to) lives on arcturus, tied to the
CORA repository's working directory. To pick it up where it left
off, SSH into arcturus, ``cd`` into the CORA clone, and pass the
session ID to ``claude --resume``:

.. code-block:: bash

   ssh 2bmb@arcturus
   cd /home/beams/2BMB/conda/cora-decarlof
   claude --resume ec161336-7b9c-4c81-b36a-2f36457e0540

Substitute the session ID above with the ID of whichever Claude
session you want to resume. ``claude`` prints the session ID at
the top of every run and stores per-session transcripts under
``~/.claude/projects/<repo-path-slug>/``, where the slug is the
CORA repository's absolute path with ``/`` replaced by ``-``. Run
``ls ~/.claude/projects/-home-beams0-2BMB-conda-cora-decarlof/``
to see the available session IDs on disk.


Notes
=====

- **When the CORA schema changed** since your last snapshot (new
  endpoints, renamed fields, changed request bodies), refresh the
  ``openapi.json`` before reloading the browser:

  .. code-block:: bash

     ~/claude/cora/swagger-ui/refresh.sh
     # or point at another CORA host:
     ~/claude/cora/swagger-ui/refresh.sh http://otherhost:8000/openapi.json

  Then hard-refresh the browser tab (Ctrl+Shift+R) to bust the
  cache.

- **Why the helper script is so short.** ``serve.sh`` is just:

  .. code-block:: bash

     #!/usr/bin/env bash
     set -euo pipefail
     cd "$(dirname "$0")"
     PORT="${1:-8080}"
     echo "Swagger UI: http://localhost:${PORT}/"
     python -m http.server "${PORT}" --bind 127.0.0.1

  The ``set -euo pipefail`` makes the script fail fast on any
  error, and the ``echo`` reminds you of the URL. Neither matters
  when you type the commands interactively, so the two-line raw
  form above is exactly equivalent.

- **State loss.** The in-memory kernel keeps the event log in
  process memory. Every Actor, Recipe, and Run you create in
  Terminal 1 is lost the moment ``uvicorn`` restarts (including on
  ``--reload`` when you edit CORA source). Terminal 2 has no such
  state; it just serves the static Swagger UI.
