===============
Data Management
===============

.. warning::

   **2026-08-14 — tomodata2 disk-array failure:** the ``/data2`` mount was
   totally lost. Everything on ``/data2`` that had **not** yet been mirrored
   to DM is permanently gone. Items on ``/data3`` and on DM were unaffected.
   Entries below tagged "**LOST**" refer to /data2 content that was not on DM
   at the time of failure. Detailed inventories: ``data2_backup_summary.pdf``
   (what was saved) and ``data2_lost_summary.pdf`` (what was lost) in
   ``/home/beams/2BMB/claude/dm/``.

This page summarizes the DM (APS Data Management) status for 2-BM datasets.

Convention: **Done** means the dataset was permanently moved from ``/local2/2BM``,
``/data2/2BM``, or ``/data3/2BM`` to ``/gdata/dm/2BM`` and the original copies were
deleted. **Pending** means the dataset still lives on one of the local disks and
has not yet been fully archived.

.. contents:: On this page
   :local:
   :depth: 2


Done — permanently on DM
========================

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Dataset
     - Size
     - Removed from
     - DM location
   * - ``2026-03-Li-1018528`` (raw)
     - 3.4 T
     - /data2/2BM/2026-03/2026-03-Li-1018528/
     - /gdata/dm/2BM/2026-03/2026-03-Li-1018528/data/
   * - ``2026-03-Li-1018528`` (trimmed h5)
     - 378 G
     - /data2/2BM/2026-03/2026-03-Li-1018528_rec/
     - /gdata/dm/2BM/2026-03/2026-03-Li-1018528/analysis/
   * - ``2026-07-Boyer-0``
     - 3.8 T
     - /local2/2BM/2026-07-Boyer-0/
     - /gdata/dm/2BM/2026-07/2026-07-Boyer-0/data/
   * - ``2026-07-Li-1014288`` (raw + rec)
     - 58 T
     - /data2/2BM/2026-07-Li-1014288{,_rec}/
     - /gdata/dm/2BM/2026-07/2026-07-Li-1014288/{data,analysis}/
   * - ``2026-07-Liu-0`` (raw + rec)
     - ~2.5 T (457 G + 2.0 T)
     - /data3/2BM/2026-07-Liu-0{,_rec}/
     - /gdata/dm/2BM/2026-07/2026-07-Liu-0/{data,analysis}/ *(3 JPG snapshots ~940 KB unique to /data3 not archived)*
   * - ``2026-07-Nikitin-0`` (raw + rec)
     - ~12 T
     - /data3/2BM/2026-07-Nikitin-0{,_rec}/
     - /gdata/dm/2BM/2026-07/2026-07-Nikitin-0/{data,analysis}/
   * - ``2026-07-Qiu-1017594`` (raw + rec)
     - ~28 T
     - /local2 + /data2/2BM/2026-07-Qiu-1017594{,_rec}/
     - /gdata/dm/2BM/2026-07/2026-07-Qiu-1017594/{data,analysis}/
   * - ``2026-07-Rippner-1011312`` (eBERlight)
     - 5.1 T
     - /data3/2BM/2026-07-Rippner-1011312/
     - uploaded to DM directly by Xiaoyang Liu (outside standard /gdata/dm/2BM path)
   * - ``2026-08-Haridy-1015116`` (raw + rec)
     - ~36 T
     - /local2 + /data2/2BM/2026-08-Haridy-1015116{,_rec}/
     - /gdata/dm/2BM/2026-08/2026-08-Haridy-1015116/{data,analysis}/


Pending — still on local disk, not fully archived
=================================================

.. note::

   Rows with an **approve →** link are already verified on DM and ready to
   delete. Click the link to send a pre-filled approval email to Francesco
   De Carlo (decarlo@anl.gov). If your browser has no email client
   configured, copy the path/size info from the row and send it via Slack
   or any other channel instead. The dataset will be moved to DM once
   approval is received.

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Dataset / Path
     - Size
     - DM status
     - Action needed
     - Confirm to move
   * - ``/local2/2BM/2026-07-Liu-0/``
     - 475 G
     - all 21 h5 on DM (byte-exact)
     - safe to ``rm -rf``; frees 475 G
     - `approve → <mailto:decarlo@anl.gov?subject=DM%20delete%20approval%3A%20%2Flocal2%2F2BM%2F2026-07-Liu-0%2F&body=I%20approve%20deletion%20of%20%2Flocal2%2F2BM%2F2026-07-Liu-0%2F%20(475%20G%2C%2021%20h5%20verified%20byte-exact%20on%20DM).>`__
   * - ``/local2/2BM/2026-07-Nikitin-0/``
     - 883 G
     - all 23 h5 on DM (byte-exact)
     - safe to ``rm -rf``; frees 883 G
     - `approve → <mailto:decarlo@anl.gov?subject=DM%20delete%20approval%3A%20%2Flocal2%2F2BM%2F2026-07-Nikitin-0%2F&body=I%20approve%20deletion%20of%20%2Flocal2%2F2BM%2F2026-07-Nikitin-0%2F%20(883%20G%2C%2023%20h5%20verified%20byte-exact%20on%20DM).>`__
   * - ``/data3/2BM/2026-07-DeCarlo-0``
     - 82 G
     - not on DM (test folder)
     - assess necessity
     - —
   * - ``/data3/Allen-NIH-mosaic_2`` (owner sboyer, raw y-segments + mosaic)
     - 36 T
     - **fully on DM** — 59,741/59,741 files byte-exact under ``/gdata/dm/2BM/2026-05/2026-05-Boyer-0/data/Allen-NIH-mosaic_2/``. DM manual experiment ``2026-05-Boyer-0``, GUP 0, PI Sarah Boyer. rsync completed 2026-08-07 21:01 (33h 43m at ~305 MB/s avg).
     - awaiting Sarah to ``rm -rf`` as ``sboyer`` (files owned by her)
     - — (waiting on Sarah)
   * - ``/data3/Allen-NIH-mosaic`` (owner tomo, zarr derivatives)
     - 18 T
     - **not backed up to DM** — derived data (zarr rewrites of the mosaic_2 content); can be regenerated from the DM copy of ``Allen-NIH-mosaic_2`` if ever needed. ~9.2 M small zarr chunk files would take days to rsync; decision was to keep this only on /data3.
     - no action — keep on /data3 as long as space permits
     - —
   * - ``/data3/vnikitin`` (Viktor's personal workspace)
     - 57 T
     - **fully on DM** — 398,021 files byte-exact under ``/gdata/dm/2BM/2026-08/2026-08-Nikitin-0/data/vnikitin_data3/``. DM manual experiment ``2026-08-Nikitin-0``, GUP 0, PI Viktor Nikitin. Completed 2026-08-14 06:20 (initial pass on tomo4 died with the host; resumed on tomdet, finished cleanly).
     - awaiting Viktor's ``rm -rf`` as ``vnikitin`` (data owned by tomo/vnikitin)
     - — (waiting on Viktor)
   * - ``/data2/vnikitin`` (Viktor's active workspace)
     - 14 T source, **~13 T saved / ~1.4 T LOST**
     - All 3 rsync passes completed before crash; ~13 T (61,036 files pass 1 + 166 files pass 2 + 119,371 files pass 3) safely under ``/gdata/dm/2BM/2026-08/2026-08-Nikitin-0/data/vnikitin_data2/``. **LOST on 2026-08-14**: active items excluded from rsync — ``20240515`` (554 G), ``iotest`` (307 G), ``iotest_buf_ups1`` (527 G), plus ``tmp/t_test.h5`` (157 MB, appeared during pass 3 scan).
     - no action; Viktor already notified of the lost active items
     - N/A — /data2 gone
   * - ``/data2/2BM/2026-07-Boyer-0`` (raw)
     - 3.8 T
     - **on DM** — verified byte-exact before crash. /data2 source LOST 2026-08-14 but DM copy at ``/gdata/dm/2BM/2026-07/2026-07-Boyer-0/data/`` is intact.
     - none — DM is authoritative
     - N/A — /data2 gone
   * - ``/data2/2BM/2026-07-Boyer-0_rec``
     - 149 G
     - **on DM** (all but a 879 B log) — /data2 source LOST 2026-08-14; DM ``/analysis`` unaffected.
     - none — DM is authoritative
     - N/A — /data2 gone

Lost to 2026-08-14 tomodata2 failure
====================================

The following ``/data2/2BM`` content had NOT been mirrored to DM at the time of the
disk-array failure and is permanently unrecoverable:

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Dataset / Path
     - Size
     - Notes
   * - ``/data2/2BM/2026-03/Noemi`` + ``Noemi_rec``
     - 230 G + 87 G
     - no DM folder; possible earlier candidate ``/gdata/dm/2BM/2026-02/2026-02-BrainNoemi-0`` never verified
   * - ``/data2/2BM/2026-07-Boyer-0_tmp`` + ``_tmp_rec``
     - 107 G + 79 G
     - was never verified against DM
   * - ``/data2/2BM/2026-02`` (Feb 2026 working folder)
     - 1.5 T
     - never inventoried per-dataset
   * - ``/data2/2BM/2026-07-Li-1014288_rec_test``
     - 480 G
     - scratch / test folder
   * - ``/data2/2BM/tmp_denose_brain``
     - 2.5 T
     - scratch folder (denoise brain)
   * - ``/data2/2BM/brain_beta`` + ``brain_delta``
     - 316 G + 633 G
     - never verified against DM
   * - ``/data2/2BM/test``, ``/data2/2BM/test2``, ``/data2/2BM/2025-06``, ``/data2/2BM/2025-12``
     - < 170 G combined
     - test / old
   * - ``/data2/vnikitin`` active items (see also main entry above)
     - ~1.4 T
     - ``20240515``, ``iotest``, ``iotest_buf_ups1``, ``tmp/t_test.h5``

Plus the /data2 unbacked content from other beamlines (``/data2/Allen-nih`` ~20 T,
``/data2/Center_of_Rotation`` 3 T, ``/data2/ESRF`` 2.1 T, ``/data2/cekmekci`` 1.4 T,
``/data2/maria`` 635 G, ``/data2/tmp_from_data3`` 354 G, ``/data2/Brain_holo`` 125 G,
``/data2/tmp`` 80 G) — see the full inventory in ``data2_lost_summary.pdf``.
