GIT
===

.. contents:: 
   :local:

Basic
-----

The git commands to update, commit and push your local repository are:: 

   $ git status
   $ git add -u
   $ git add new_files
   $ git commit -m "my commit comment"
   $ git push origin master

then go to the github repository your fork originated from and create a pull request, then merge it.

Remove large files
------------------

To remove large files from a github repository 

Let's say you want to remove all tiff files from the all commit history of a github repository e.g. https://github.com/decarlof/holotomocupy_bq.git.  First you want to delete the unwanted files from the last commit:

::

   $ git clone https://github.com/decarlof/holotomocupy_bq.git
   $ cd holotomocupy_bq
   $ find . -type f \( -iname \*.tif -o -iname \*.tiff \)

   ./examples_synthetic/data/beta-chip-192.tiff
   ./examples_synthetic/data/delta-chip-192.tiff
   ./examples_synthetic/data/prb_id16a/prb_abs_1024.tiff
   ./examples_synthetic/data/prb_id16a/prb_abs_192.tiff
   ./examples_synthetic/data/prb_id16a/prb_abs_2048.tiff
   ./examples_synthetic/data/prb_id16a/prb_abs_256.tiff
   ./examples_synthetic/data/prb_id16a/prb_abs_384.tiff
   ./examples_synthetic/data/prb_id16a/prb_abs_512.tiff
   ./examples_synthetic/data/prb_id16a/prb_phase_1024.tiff
   ./examples_synthetic/data/prb_id16a/prb_phase_192.tiff
   ./examples_synthetic/data/prb_id16a/prb_phase_2048.tiff
   ./examples_synthetic/data/prb_id16a/prb_phase_256.tiff
   ./examples_synthetic/data/prb_id16a/prb_phase_384.tiff
   ./examples_synthetic/data/prb_id16a/prb_phase_512.tiff

   $ rm -rf ./examples_synthetic/data/beta-chip-192.tiff
   $ rm -rf ./examples_synthetic/data/delta-chip-192.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_abs_1024.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_abs_192.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_abs_2048.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_abs_256.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_abs_384.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_abs_512.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_phase_1024.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_phase_192.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_phase_2048.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_phase_256.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_phase_384.tiff
   $ rm -rf ./examples_synthetic/data/prb_id16a/prb_phase_512.tiff
   $ git add -u
   $ git commit -m "removed tiff files"
   $ git push




Install `bfg repo cleaner <https://rtyley.github.io/bfg-repo-cleaner/>`_ to obtain the latest version of bfg then
clone the repository using the --mirror option:

::

   $ git clone --mirror https://github.com/decarlof/holotomocupy_bq.git
   $ java -jar ~/Downloads/bfg-1.14.0.jar -D "*.tiff" holotomocupy_bq.git/
   Using repo : /Users/decarlo/conda/holotomocupy_bq.git

   Found 35 objects to protect
   Found 2 commit-pointing refs : HEAD, refs/heads/master

   Protected commits
   -----------------

   These are your protected commits, and so their contents will NOT be altered:

    * commit 626411d5 (protected by 'HEAD')

   Cleaning
   --------

   Found 65 commits
   Cleaning commits:       100% (65/65)
   Cleaning commits completed in 134 ms.

   Updating 1 Ref
   --------------

      Ref                 Before     After   
      ---------------------------------------
      refs/heads/master | 626411d5 | 35529ff1

   Updating references:    100% (1/1)
   ...Ref update completed in 14 ms.

   Commit Tree-Dirt History
   ------------------------

      Earliest                                              Latest
      |                                                          |
      .......DDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDDm

      D = dirty commits (file tree fixed)
      m = modified commits (commit message or parents changed)
      . = clean commits (no changes to file tree)

                              Before     After   
      -------------------------------------------
      First modified commit | c8f5ff5f | 8a11c799
      Last dirty commit     | 0dd743b5 | 7307f3af

   Deleted files
   -------------

      Filename                  Git id                                
      ----------------------------------------------------------------
      beta-chip-192.tiff      | 5817f483 (27.0 MB)                    
      data_chip_384_0.tiff    | d134a399 (50.6 MB)                    
      data_chip_384_1.tiff    | 88452e45 (50.6 MB)                    
      data_chip_384_2.tiff    | d89fb163 (50.6 MB)                    
      data_chip_384_3.tiff    | a6e464f7 (50.6 MB)                    
      data_phantom_384_0.tiff | ef07a76a (576.3 KB)                   
      data_phantom_384_1.tiff | a0c64c93 (576.3 KB)                   
      data_phantom_384_2.tiff | 6be72afc (576.3 KB)                   
      data_phantom_384_3.tiff | 02530ed1 (576.3 KB)                   
      delta-chip-192.tiff     | af0b8a5c (27.0 MB)                    
      flat_chip_384_0.tiff    | 655cf599 (576.3 KB)                   
      flat_chip_384_1.tiff    | f3f1cca6 (576.3 KB)                   
      flat_chip_384_2.tiff    | 4e0900f2 (576.3 KB)                   
      flat_chip_384_3.tiff    | d35c35ea (576.3 KB)                   
      flat_phantom_384_0.tiff | 9fc7e4af (576.3 KB)                   
      ...


   In total, 153 object ids were changed. Full details are logged here:

      /Users/decarlo/conda/holotomocupy_bq.git.bfg-report/2024-06-18/14-36-21

   BFG run is complete! When ready, run: git reflog expire --expire=now --all && git gc --prune=now --aggressive

::

   $ cd holotomocupy_bq.git/
   $ git reflog expire --expire=now --all && git gc --prune=now --aggressive
   $ git push


The repository on github is now clear of all .tiff files from its history. You can clone again with:

::

   $ git clone https://github.com/decarlof/holotomocupy_bq_without_tiff.git

and delete the old holotomocupy_bq folder.




