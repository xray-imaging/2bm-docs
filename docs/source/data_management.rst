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
     - 38.5 T
     - **fully on DM in two places, verified 1:1** — ``2026-05-Boyer-0/data/Allen-NIH-mosaic_2/`` (original) and ``2026-08-ImagingStaff-0/data/data3/Allen-NIH-mosaic_2/`` (consolidated). All three (/data3, Boyer-0, ImagingStaff) show 59,741 files exact match. Boyer-0 to be retired via dmadmin on demand.
     - awaiting Sarah to ``rm -rf`` as ``sboyer`` (files owned by her)
     - — (waiting on Sarah)
   * - ``/data3/Allen-NIH-mosaic`` (owner tomo, zarr derivatives)
     - 18 T
     - **not backed up to DM — explicitly excluded from ImagingStaff** — derived data (zarr rewrites of the mosaic_2 content); regenerable from the DM copy of ``Allen-NIH-mosaic_2``. ~9.2 M small zarr chunk files would take days to rsync; decision was to keep this only on /data3.
     - no action — keep on /data3 as long as space permits
     - —
   * - ``/data3/vnikitin`` (Viktor's personal workspace)
     - 30.35 T current / 57 T pre-reorg
     - **fully on DM in two places** — (1) ``2026-08-Nikitin-0/data/vnikitin_data3/`` (398 K files, pre-Aug-22 snapshot, KEPT as historical archive of content Viktor deleted during his reorganization), and (2) ``2026-08-ImagingStaff-0/data/data3/vnikitin/`` (~202 K files, clean mirror of current /data3 state, **byte-identical to /data3 as of 2026-08-26**). See *Viktor Nikitin workspace reorganization* in the shared backup section below.
     - Viktor is actively using /data3/vnikitin; no delete pending
     - N/A — active workspace

   * - ``/data2/vnikitin`` (Viktor's active workspace)
     - 14 T source, **~13 T saved / ~1.4 T LOST**
     - All 3 rsync passes completed before crash; ~13 T (61,036 files pass 1 + 166 files pass 2 + 119,371 files pass 3) safely under ``/gdata/dm/2BM/2026-08/2026-08-Nikitin-0/data/vnikitin_data2/``. **LOST on 2026-08-14**: active items excluded from rsync — ``20240515`` (554 G), ``iotest`` (307 G), ``iotest_buf_ups1`` (527 G), plus ``tmp/t_test.h5`` (157 MB, appeared during pass 3 scan). **Nikitin-0 copy DM→DM folded** into ``2026-08-ImagingStaff-0/data/data2/vnikitin/`` (Stream B completed 2026-08-19, 14.2 T byte-exact).
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

Shared staff/project backup — ``2026-08-ImagingStaff-0``
========================================================

Started **2026-08-18** to consolidate all non-beamline dirs on ``/data3`` (and
later ``/data2`` once IT clears it after the 2026-08-14 failure) into a single
yearly shared DM experiment. Purpose: provide DM archival for staff personal
workspaces (sboyer, vnikitin, etc.) and cross-cutting project dirs (ESRF,
Allen-NIH-\*, TMP_\*) that don't fit under a beamline GUP. **Yearly rollover:**
this experiment covers Aug–Dec 2026; a fresh ``2027-01-ImagingStaff-0`` will
be created in January.

.. note::

   **ACL widening.** ``2026-08-ImagingStaff-0`` grants read/write to all 9
   imaging group staff badges (Fezzaa, Kastengren, Shevchenko, Clark, Boyer,
   Mittone, Tang, Ekmekci, De Carlo). This is wider than the per-user rescue
   experiments (``2026-08-Nikitin-0``, ``2026-05-Boyer-0``) which will be
   retired once ImagingStaff verifies. Sarah and Viktor have been notified.

Execution: **two parallel streams**

- **Stream A (serial):** fresh rsyncs from ``/data3`` → ``ImagingStaff/data/data3/``
  for all live sources. One item at a time, ~350 MB/s per stream.
- **Stream B (side channel):** DM→DM copy of ``Nikitin-0/data/vnikitin_data2/`` →
  ``ImagingStaff/data/data2/vnikitin/`` (only surviving copy of the destroyed
  /data2/vnikitin workspace). Runs on a different NFS server than Stream A, no
  bandwidth contention, ~570 MB/s.

All rsyncs use ``rsync -rt --partial --info=progress2 --stats --chmod=Dg+w --exclude='.Trash-*'``.
The ``--chmod=Dg+w`` flag keeps new dirs group-writable so the ACL mask stays
``rwx`` and future delta rsyncs don't hit "mkstemp Permission denied" errors on
tight-mask subdirs.

Current status (all complete as of 2026-08-23)
----------------------------------------------

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Source
     - Files
     - Size
     - DM destination under ``2026-08-ImagingStaff-0/``
     - Status
   * - ``/data3/sboyer``
     - 9.4 M initial → growing
     - 44.7 T initial → 52 T (2026-09-01)
     - ``data/data3/sboyer/``
     - ✅ initial rsync DONE 1:1 verified; delta rsync 2026-09-01 added ~4.9 T of Sarah's marmoset_brain reconstructions
   * - ``/data3/sboyer_rec``
     - 7,681
     - ~1.2 T
     - ``data/data3/sboyer_rec/``
     - ✅ DONE, 1:1 verified
   * - ``/data3/vnikitin``
     - ~202,000 → growing
     - 30.35 T → 34.86 T (2026-09-01)
     - ``data/data3/vnikitin/``
     - ✅ DONE + on-demand delta rsyncs (2026-08-24, 25, 26, and 2026-09-01). Delta rsync #4 (2026-09-01) added ~5.1 T; now includes exclude filters ``tmp*/TMP*/*tmp/*TMP`` per Viktor's request. See *Viktor workspace reorganization* below.
   * - ``/data3/Allen-NIH-mosaic_2``
     - 59,741
     - 38.5 T
     - ``data/data3/Allen-NIH-mosaic_2/``
     - ✅ DONE, 1:1 verified (Stream D DM→DM from Boyer-0, then Stream A verify)
   * - ``/data3/ESRF``
     - 5,802
     - (small)
     - ``data/data3/ESRF/``
     - ✅ DONE, 1:1 verified
   * - ``/data3/TMP_BRAIN_ESRF``
     - 0
     - empty
     - ``data/data3/TMP_BRAIN_ESRF/``
     - ✅ empty dir mirrored
   * - ``/data3/TMP_YALE``
     - 2,261,466
     - (large)
     - ``data/data3/TMP_YALE/``
     - ✅ DONE, 1:1 verified
   * - ``Nikitin-0/data/vnikitin_data2/`` (DM→DM)
     - ~192,000
     - 14.2 T
     - ``data/data2/vnikitin/``
     - ✅ DONE 2026-08-19 (Stream B, only surviving copy of destroyed /data2/vnikitin)
   * - ``/data3/Allen-NIH-mosaic`` (18 T zarr)
     - ~9.2 M
     - 18 T
     - — (**excluded**)
     - regenerable from Allen-NIH-mosaic_2; keep on /data3 only

Total transferred to ImagingStaff: **~170 T** across 5 parallel/sequential streams
over 2026-08-18 through 2026-08-23. Actual wall-clock exceeded initial estimates
because Viktor was actively writing to ``/data3/vnikitin`` throughout (see below).

Viktor Nikitin workspace reorganization
---------------------------------------

Between the ``2026-08-Nikitin-0`` rescue snapshot (2026-08-14) and the
ImagingStaff rsync (2026-08-22), Viktor made a major reorganization of
``/data3/vnikitin``:

- Top-level dirs went from **97** (in Nikitin-0) to **14** (current /data3)
- ~57 T of older content was deleted from /data3 (reconstructions,
  experimental variants, older ESRF / atomium runs, etc.)
- The active-work directory ``20260416/`` (25 subdirs) was ``mv``'d from
  ``/data3/vnikitin/20260416/`` to ``/data3/vnikitin/ESRF/20260416/``
- 5 new top-level dirs added: ``APS_IRI``, ``brain_dose_study``,
  ``dose_study``, ``holobrain_syn``, ``mosaic_brain``

**Consequence:** ``ImagingStaff/data/data3/vnikitin/`` holds a clean mirror
of the **current** /data3/vnikitin state (30.35 T, ~202 K files), confirmed
byte-identical to /data3 on 2026-08-26 after three on-demand delta rsyncs
that closed the gap left by Viktor's live writes during the initial pass.
The **pre-reorg** state — including the 57 T Viktor deleted — survives
**only** in ``2026-08-Nikitin-0/data/vnikitin_data3/`` (398 K files). For
this reason ``2026-08-Nikitin-0`` is **kept as historical archive** and is
**not** retired.

Notes
-----

- ``/data2`` has been rebuilt as RAID5 (was RAID0, lost in the 2026-08-14
  failure). Currently essentially empty; four top-level dirs seeded for the
  beamlines (``2BM``, ``7BM``, ``19BM``, ``32ID``). Once staff repopulate it,
  weekly rsyncs will pick up the new content into
  ``ImagingStaff/data/data2/``.
- ``2026-05-Boyer-0`` — verified 1:1 against ImagingStaff (59,741 files exact
  match on all three: /data3, Boyer-0, ImagingStaff). **Ready to retire** via
  dmadmin (not ``rm -rf`` on ``/gdata/dm/``, which would orphan DM metadata).
  Retirement will be triggered on demand.
- ``2026-08-Nikitin-0`` — **kept as historical archive** (see reorganization
  section above); sole surviving copy of the pre-Aug-22 /data3/vnikitin state.
  Small ACL preserved (Viktor + Francesco).
- Refresh cadence: **weekly weekend delta rsyncs** from ``/data2`` +
  ``/data3`` into the current year's experiment. Currently run **on demand**
  until the process is stable; automated cron to follow. No ``--delete`` — DM
  keeps history of anything users removed since last snapshot.
- Before deleting any ``/data3`` source, spot-check against the ImagingStaff
  destination using the pattern in ``/home/beams/2BMB/claude/dm/verify_all_subtrees.sh``
  (path+size 1:1 comparison via ``find | sort | comm``).

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
