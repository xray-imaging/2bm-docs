=======================================
Install CORA into a dedicated conda env
=======================================

Purpose
=======

Create a self-contained Python 3.13 environment for CORA and install
every runtime + development dependency into it. The upstream README
uses ``uv`` (a fast Python package manager) which creates a project-
local ``.venv`` under ``apps/api/``; we keep ``uv`` because every
``make`` target is written against it, but we host the Python
interpreter in a conda env so it lives alongside every other conda
env on the machine instead of in the CORA tree.

Because ``$HOME`` is NFS-shared between tocai and arcturus, one
install is visible on both hosts.


Prerequisites
=============

- Miniconda or Anaconda installed at ``~/conda/anaconda``
  (or wherever your ``conda`` command resolves to).
- Public-network access on tocai (needed to reach PyPI and GitHub).
- A GitHub account with a fork of the upstream CORA repository.
  For 2-BM the fork lives at https://github.com/decarlof/cora.


Steps
=====

1. **Fork upstream CORA** (one-time, via the GitHub web UI). Visit
   https://github.com/xmap/cora and click **Fork** to create your
   own copy. The 2-BM fork is https://github.com/decarlof/cora;
   substitute your own fork URL in the next step.

2. **Clone your fork** on tocai. The 2-BM install uses
   ``~/conda/cora-decarlof`` as the clone directory (visible from
   both tocai and arcturus via the shared NFS ``$HOME``):

   .. code-block:: bash

      cd ~/conda
      git clone https://github.com/decarlof/cora.git cora-decarlof
      cd cora-decarlof
      git remote add upstream https://github.com/xmap/cora.git
      git remote -v

   The two-remote setup gives you a clean contribution loop:

   - Local fixes are committed on a branch of the fork
     (``origin``) and pushed there for testing on the 2-BM
     install. They can live on the fork indefinitely if they are
     beamline-specific.
   - Anything worth sharing upstream is opened as a Pull Request
     from your fork's branch into ``xmap/cora`` via the GitHub UI.
     The fork acts as the staging ground; upstream sees only what
     is ready.
   - Periodic sync with upstream keeps the fork current:

     .. code-block:: bash

        git fetch upstream
        git rebase upstream/main         # when you want the latest
        git push origin main --force-with-lease   # update the fork

3. Create the conda env (run on tocai; it will be visible on
   arcturus once created):

   .. code-block:: bash

      conda create -n cora python=3.13
      conda activate cora

4. Install ``uv`` inside the env. ``pip install`` is used here (not
   ``conda install``) because ``uv`` is what CORA's Makefile drives:

   .. code-block:: bash

      pip install uv

5. Install every CORA dependency into the active conda env. The
   ``--active`` flag is critical — without it, ``uv`` would create a
   nested ``apps/api/.venv`` and ignore the conda env:

   .. code-block:: bash

      cd /home/beams0/2BMB/conda/cora-decarlof/apps/api
      uv sync --all-extras --active

   ``--all-extras`` pulls two optional dependency groups defined in
   ``apps/api/pyproject.toml``:

   - ``bo`` — BoTorch + torch + gpytorch. Several hundred MB, used
     only by the Bayesian-optimization deciders. Safe to skip if
     you are not running GP-steered autonomous experiments (use
     ``uv sync --extra dev --active`` instead).
   - ``tango`` — PyTango, used only by the Tango-floor adapter
     (ESRF / MAX IV). Not needed at 2-BM.


Verify
======

.. code-block:: bash

   conda activate cora
   which python           # ${CONDA_PREFIX}/bin/python
   python --version       # Python 3.13.x
   uv --version

The ``which python`` output must point inside the conda env's ``bin``
directory. If it points at ``apps/api/.venv/bin/python`` or the
system Python, the ``--active`` flag was missing from ``uv sync`` and
the env is misconfigured.


Notes
=====

- **Non-Python tools not installed by the above.** Docker and Atlas
  are only needed for the Postgres-backed workflow (``make db-up``,
  ``make migrate-apply``). This section deliberately skips both;
  see :doc:`item_003` for the Docker-free alternative.
- **Warning about CUDA.** If you install with ``--all-extras``,
  PyTorch will probe for a CUDA driver at test time and print a
  UserWarning if the driver is old. This is harmless — torch falls
  back to CPU and the BoTorch decider works on CPU for the small
  problems CORA hands it. Drop ``--all-extras`` if the warning
  bothers you.
