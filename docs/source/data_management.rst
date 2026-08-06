===============
Data Management
===============

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
   * - ``2026-07-Nikitin-0`` (raw + rec)
     - ~12 T
     - /data3/2BM/2026-07-Nikitin-0{,_rec}/
     - /gdata/dm/2BM/2026-07/2026-07-Nikitin-0/{data,analysis}/
   * - ``2026-07-Qiu-1017594`` (raw + rec)
     - ~28 T
     - /local2 + /data2/2BM/2026-07-Qiu-1017594{,_rec}/
     - /gdata/dm/2BM/2026-07/2026-07-Qiu-1017594/{data,analysis}/
   * - ``2026-08-Haridy-1015116`` (raw + rec)
     - ~36 T
     - /local2 + /data2/2BM/2026-08-Haridy-1015116{,_rec}/
     - /gdata/dm/2BM/2026-08/2026-08-Haridy-1015116/{data,analysis}/


Pending — still on local disk, not fully archived
=================================================

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Dataset / Path
     - Size
     - DM status
     - Action needed
   * - ``/local2/2BM/2026-07-Liu-0/``
     - 475 G
     - all 21 h5 on DM (byte-exact)
     - safe to ``rm -rf``; frees 475 G
   * - ``/local2/2BM/2026-07-Nikitin-0/``
     - 883 G
     - all 23 h5 on DM (byte-exact)
     - safe to ``rm -rf``; frees 883 G
   * - ``/data3/2BM/2026-07-Rippner-1011312``
     - 5.1 T
     - **not on DM** — /local2 copy was deleted, so /data3 is the only surviving copy
     - decide: upload to DM or accept as sole archive
   * - ``/data3/2BM/2026-07-DeCarlo-0``
     - 82 G
     - not on DM (test folder)
     - assess necessity
   * - ``/data3/2BM/2026-07-Liu-0`` + ``_rec``
     - 457 G raw + 2.0 T rec
     - not fully backed up on DM
     - upload to DM before delete
   * - ``/data2/2BM/2026-03/2026-03-Li-1018528`` (raw)
     - 3.4 T
     - all 66 h5 on DM (DM has 238 h5 — superset)
     - safe to ``rm -rf``
   * - ``/data2/2BM/2026-03/Noemi`` + ``Noemi_rec``
     - 230 G + 87 G
     - no DM folder; possible candidate ``/gdata/dm/2BM/2026-02/2026-02-BrainNoemi-0``
     - manual verification needed
   * - ``/data2/2BM/2026-07-Boyer-0`` (raw)
     - 3.8 T
     - fully on DM (byte-exact)
     - safe to ``rm -rf``; frees 3.8 T
   * - ``/data2/2BM/2026-07-Boyer-0_rec``
     - 149 G
     - partial; DM ``/analysis`` has 213 GB more content, /data2 has 1 log file variant
     - safe to ``rm -rf`` (only 879 B log unique to /data2)
   * - ``/data2/2BM/2026-07-Boyer-0_tmp`` + ``_tmp_rec``
     - 107 G + 79 G
     - not verified against DM
     - assess necessity
   * - ``/data2/2BM/2026-02`` (Feb 2026 working folder)
     - 1.5 T
     - not verified per-dataset
     - inventory + verify
   * - ``/data2/2BM/2026-07-Li-1014288_rec_test``
     - 480 G
     - scratch/test folder
     - assess necessity
   * - ``/data2/2BM/tmp_denose_brain``
     - 2.5 T
     - scratch folder
     - assess necessity
   * - ``/data2/2BM/brain_beta`` + ``brain_delta``
     - 316 G + 633 G
     - not verified against DM
     - assess
   * - ``/data2/2BM/test``, ``/data2/2BM/test2``, ``/data2/2BM/2025-06``, ``/data2/2BM/2025-12``
     - < 130 G combined
     - test/old
     - clean-up candidates
