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
     - rsync in progress → ``/gdata/dm/2BM/2026-08/2026-08-Nikitin-0/data/vnikitin_data3/``. DM manual experiment ``2026-08-Nikitin-0``, GUP 0, PI Viktor Nikitin. Started 2026-08-11; ~420 MB/s solo (halved while sibling rsync runs); ETA ~38 h solo.
     - wait for rsync + verify, then ``rm -rf`` (Viktor approved archival)
     - — (transfer in progress)
   * - ``/data2/vnikitin`` (Viktor's active workspace — **INACTIVE items only, ~8.7 T of 14 T**)
     - 8.7 T
     - rsync in progress → ``/gdata/dm/2BM/2026-08/2026-08-Nikitin-0/data/vnikitin_data2/`` (same DM experiment). Started 2026-08-11; only 13 subdirs/files idle for >30 d (``ESRF``, ``ctxl``, ``data_brainY350a_dist1234.h5``, ``Chawla{,_rec}``, ``Y350a{,_noise}``, ``correct_correct3D.txt``, plus 5 empty dirs). ACTIVE items (``20240515``, ``iotest*``, ``tmp``, ``ctxl_rec*``, ``Y350c_rec_new_cubic_deform`` — ~5.3 T combined) are NOT being transferred yet — Viktor still writing.
     - wait for rsync + verify; then ask Viktor about ACTIVE items before final delete
     - — (transfer in progress)
   * - ``/data3/2BM/2026-07-Liu-0`` + ``_rec``
     - 457 G raw + 2.0 T rec
     - fully on DM (raw 20/21 h5 byte-exact, rec 50,016/50,016 files byte-exact)
     - safe to ``rm -rf``; frees ~2.5 T
     - `approve → <mailto:decarlo@anl.gov?subject=DM%20delete%20approval%3A%20%2Fdata3%2F2BM%2F2026-07-Liu-0%20and%20_rec&body=I%20approve%20deletion%20of%20%2Fdata3%2F2BM%2F2026-07-Liu-0%20and%20%2Fdata3%2F2BM%2F2026-07-Liu-0_rec%20(~2.5%20T%2C%20fully%20on%20DM%20byte-exact).>`__
   * - ``/data2/2BM/2026-03/Noemi`` + ``Noemi_rec``
     - 230 G + 87 G
     - no DM folder; possible candidate ``/gdata/dm/2BM/2026-02/2026-02-BrainNoemi-0``
     - manual verification needed
     - —
   * - ``/data2/2BM/2026-07-Boyer-0`` (raw)
     - 3.8 T
     - fully on DM (byte-exact)
     - safe to ``rm -rf``; frees 3.8 T
     - `approve → <mailto:decarlo@anl.gov?subject=DM%20delete%20approval%3A%20%2Fdata2%2F2BM%2F2026-07-Boyer-0&body=I%20approve%20deletion%20of%20%2Fdata2%2F2BM%2F2026-07-Boyer-0%20(3.8%20T%20raw%2C%20fully%20on%20DM%20byte-exact).>`__
   * - ``/data2/2BM/2026-07-Boyer-0_rec``
     - 149 G
     - partial; DM ``/analysis`` has 213 GB more content, /data2 has 1 log file variant
     - safe to ``rm -rf`` (only 879 B log unique to /data2)
     - `approve → <mailto:decarlo@anl.gov?subject=DM%20delete%20approval%3A%20%2Fdata2%2F2BM%2F2026-07-Boyer-0_rec&body=I%20approve%20deletion%20of%20%2Fdata2%2F2BM%2F2026-07-Boyer-0_rec%20(149%20G%2C%20DM%20has%20everything%20except%20a%20single%20879%20B%20log%20file).>`__
   * - ``/data2/2BM/2026-07-Boyer-0_tmp`` + ``_tmp_rec``
     - 107 G + 79 G
     - not verified against DM
     - assess necessity
     - —
   * - ``/data2/2BM/2026-02`` (Feb 2026 working folder)
     - 1.5 T
     - not verified per-dataset
     - inventory + verify
     - —
   * - ``/data2/2BM/2026-07-Li-1014288_rec_test``
     - 480 G
     - scratch/test folder
     - assess necessity
     - —
   * - ``/data2/2BM/tmp_denose_brain``
     - 2.5 T
     - scratch folder
     - assess necessity
     - —
   * - ``/data2/2BM/brain_beta`` + ``brain_delta``
     - 316 G + 633 G
     - not verified against DM
     - assess
     - —
   * - ``/data2/2BM/test``, ``/data2/2BM/test2``, ``/data2/2BM/2025-06``, ``/data2/2BM/2025-12``
     - < 130 G combined
     - test/old
     - clean-up candidates
     - —
