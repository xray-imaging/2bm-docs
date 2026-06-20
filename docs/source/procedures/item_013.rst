===================================================
NV200D triggered-step buffer programming
===================================================

Load the per-axis coded-aperture position list into the two
Piezosystem Jena NV200D/NET controllers and arm them so each
rising edge on TRG IN advances to the next position in the buffer.
Run once at session setup (or whenever the random list needs
regenerating); the controllers then drive themselves frame-by-frame
from the FPGA trigger for the rest of the session.


Name
----

``nv200_trigger_step``


Source
------

- **Implementation**: `procedures/nv200_trigger_step.py
  <https://github.com/decarlof/2bm-procedures/blob/main/procedures/nv200_trigger_step.py>`__
- **Release notes**: `2bm-procedures CHANGELOG
  <https://github.com/decarlof/2bm-procedures/blob/main/CHANGELOG.md>`__

Single Python script that connects to both NV200D controllers in
parallel (asyncio), generates the per-axis position list, loads it
into each controller's waveform buffer, and arms the FPGA-trigger
advance. A second variant lives in
``/home/beams/2BMB/conda/sandbox/nv200/nv200_trigger_step.py`` —
that one uses raw Telnet and is kept as a reference implementation
of the vendor's NV200/D NET command vocabulary; it is NOT the
operationally-blessed script.


Devices
-------

- :doc:`../manual/item_020`: ``CodedApertureFineDrive_X``
  (Piezosystem Jena NV200D/NET at ``10.54.113.126``)
- :doc:`../manual/item_020`: ``CodedApertureFineDrive_Y``
  (Piezosystem Jena NV200D/NET at ``10.54.113.125``)
- :doc:`../manual/item_020`: the coded-aperture XY flexure stage
  itself (Piezosystem Jena ``nanoSXY 120 CAP``, T-223-06D)
- ``JenaNV200D`` EPICS IOC on ``arcturus`` — must be **stopped**
  for the duration of the script (one-Telnet-connection-at-a-time
  constraint on the NV200D)


Preconditions
-------------

- **EPICS IOC `JenaNV200D` is stopped.** The NV200D allows only
  one Telnet connection at a time; if the IOC holds the connection,
  the script cannot connect.
- **Network reachable to both controllers.** ``ping 10.54.113.126``
  and ``ping 10.54.113.125`` should both succeed from ``arcturus``.
- **Python environment has the `nv200` library installed.**
  ``pip install nv200 numpy`` once per environment.
- **Coded-aperture stage is mechanically aligned in the beam path.**
  The position list spans the full 0–100 µm closed-loop stroke; if
  the stage is mis-aligned the random walk will produce nonsense
  aperture positions even though the script runs cleanly.


Parameters
----------

.. list-table::
   :header-rows: 1
   :widths: 20 20 10 50

   * - Argument
     - Type
     - Default
     - Description
   * - ``--n N``
     - integer, 1 ≤ N ≤ 1024
     - 256
     - Number of positions to load into each controller's waveform
       buffer. 1024 is the NV200D hardware maximum.
   * - ``--random``
     - flag (no value)
     - off
     - If set, positions are uniformly sampled from the 0–100 µm
       stroke (compressive-sensing dithered sampling). If unset,
       positions are evenly spaced (``numpy.linspace``).


Steps
-----

.. list-table::
   :header-rows: 1
   :widths: 5 30 65

   * - #
     - Action
     - Command / call
   * - 1
     - Stop the ``JenaNV200D`` EPICS IOC (via the ``iocs_start``
       MEDM screen, or whichever IOC-control surface the
       deployment uses).
     - operator-side; see :doc:`../ops/item_028`
   * - 2
     - Change directory to the procedure script location.
     - ``cd <path-to-2bm-procedures>/procedures`` (e.g.
       ``cd ~/conda/2bm-procedures-decarlof/procedures`` or
       wherever the repo is checked out)
   * - 3
     - Run the script with the desired parameters.
     - ``python nv200_trigger_step.py [--n N] [--random]``
   * - 4
     - Wait for the "Running. Each rising edge on TRG IN (I/O
       connector pin 3) steps to the next position." line. The
       script blocks here.
     - watch console output
   * - 5
     - (Optional) verify the saved position files match the
       intended sampling pattern.
     - ``head -5 positions_x.txt`` (random will look random;
       linspace will be evenly spaced)
   * - 6
     - Run the tomography fly-scans. Each FPGA trigger pulse
       advances both controllers to their next position.
     - operator-side; standard tomoscan acquisition
   * - 7
     - When scans are complete, return to the script's terminal
       and press Enter.
     - keyboard
   * - 8
     - Restart the ``JenaNV200D`` EPICS IOC so the rest of the
       control system can address the controllers again.
     - operator-side; see :doc:`../ops/item_028`


Postconditions
--------------

On a successful run:

- Both NV200D controllers have their waveform buffer loaded with
  N positions in 0–100 µm closed-loop stroke.
- ``TriggerInFunction = WAVEFORM_STEP`` on both controllers (the
  device-side trigger arms the per-edge step advance).
- ``positions_x.txt`` and ``positions_y.txt`` saved to the current
  working directory as a record of the position list actually
  loaded (so reconstructions later have the per-frame coded-
  aperture position the scan used).

After the operator presses Enter and the script exits cleanly:

- Both controllers have ``TriggerInFunction = DISABLED`` and the
  waveform generator stopped (direct command control restored).
- Telnet connections closed; the ``JenaNV200D`` EPICS IOC can be
  restarted to reclaim the connections.

(The waveform buffer contents persist in RAM until the controller
is power-cycled; the script's optional ``save_to_eeprom()`` path
can persist the buffer across power cycles if needed.)


Failure modes
-------------

.. list-table::
   :header-rows: 1
   :widths: 35 65

   * - Symptom
     - Recovery
   * - Step 3 raises ``ConnectionRefusedError`` / Telnet refused
     - The ``JenaNV200D`` EPICS IOC is still running. Stop it
       (precondition 1) and re-run.
   * - Step 3 raises ``ValueError: position outside actuator
       range``
     - The position list contains values outside 0–100 µm. Should
       only happen if the stroke limits the controller reports
       (``posmin`` / ``posmax``) have been edited; verify with
       ``caget`` after restarting the IOC.
   * - Step 3 raises ``ValueError: Maximum 1024 positions``
     - ``--n`` larger than 1024. Reduce to the device maximum.
   * - Network timeout to one of the IPs
     - Check ``ping 10.54.113.126`` and ``10.54.113.125`` from
       ``arcturus``. If unreachable, check the
       Piezosystem-Jena-controller-side network configuration
       (per :doc:`../ops/item_028` Network configuration section).
   * - Script runs but tomography acquisitions do not advance the
       piezo positions during fly-scan
     - The FPGA-output-to-NV200D-TRG-IN cable mapping may be
       wrong. See :doc:`../ops/item_028` FPGA-trigger-integration
       section and the related open question on the cora
       deployment-questions page (PIEZO-5).
   * - Script exits cleanly but ``positions_*.txt`` not saved
     - The script must be run from a writable directory. ``cd`` to
       a writable location (step 2) before running.


Operator walkthrough
--------------------

See :doc:`../ops/item_028` for the full operator-side walk-through
including IOC stop/start, MEDM screens, the FPGA trigger
integration, the softGlueZynq GateDelay configuration, and the
Lantronix XPort flow-control settings. This procedure page covers
only the script invocation and its parameters; everything else
about operating the NV200D at 2-BM is in ``item_028``.


Notes
-----

- The 1024-position cap is the NV200D's onboard arbitrary-waveform
  generator buffer size, not a 2-BM-specific limit. 256 positions
  per axis is the operational default at 2-BM.
- ``--random`` is the current operational mode for compressive-
  sensing dithered tomography reconstructions; ``--linspace``
  (default if the flag is omitted) produces a deterministic raster.
- For cora-side modelling notes (the two-controller architecture,
  per-axis IPs, and the per-Run provenance value of the
  ``positions_*.txt`` snapshot), see the open cora questions PIEZO-1
  through PIEZO-5 on the
  `2-BM open-questions page <https://xmap.github.io/cora/deployments/2-bm/questions/>`__.
