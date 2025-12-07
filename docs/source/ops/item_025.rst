=================
FDT data transfer
=================

`Fast Data Transfer (FDT) <https://fast-data-transfer.github.io>`_ is an
open-source application for high-throughput data transfer, capable of
reading and writing at disk speed over wide-area networks (using standard
TCP).

Linux to Linux
==============

At the APS, FDT is available as ``/APSshare/bin/fdt.jar``. In the examples
below, replace ``/APSshare/bin/`` with the directory where
``fdt.jar`` is installed.

File copy
---------

To copy files from the data-collection machine (``pg10ge``) to the
data-analysis machine (``tomo1``):

1. Start the FDT server on ``tomo1``::

     [tomo@tomo1]$ java -jar /APSshare/bin/fdt.jar -S

2. Start the data transfer from ``pg10ge`` to ``tomo1``:

   Syntax::

     [user2bmb@pg10ge]$ java -jar /APSshare/bin/fdt.jar -c {remote_server} -d {remote_dir} {local_fname}

   Example::

     [user2bmb@pg10ge]$ java -jar /APSshare/bin/fdt.jar -c tomo1 -d /data/2022-11/Kemner/ /local/data/2022-11/Kemner/*.h5


Directory copy
--------------

To copy an entire directory between two Linux computers, for example from
``tomodata1`` to ``tomodata2``:

1. Start the FDT server on ``tomodata2``::

     [tomo@tomodata2]$ java -jar /APSshare/bin/fdt.jar -S

2. Start the data transfer from ``tomodata1`` to ``tomodata2``:

   Syntax (recursive copy)::

     [tomo@tomodata1]$ java -jar /APSshare/bin/fdt.jar -c {remote_server} -r -d {remote_dir} {local_fname}

   Example::

     [tomo@tomodata1]$ java -jar /APSshare/bin/fdt.jar -c tomodata2 -r -d /data2/tomodata1_backup /data/Lu-2023-03/


Verify transfer
---------------

Verify the total size of the transferred directory::

  [tomo@tomodata2]$ du /data2/tomodata1_backup/Lu-2023-03/

Verify the number of files::

  [tomo@tomodata2]$ find /data2/tomodata1_backup/Lu-2023-03/ -type f | wc -l


Windows to Linux
================

To copy data from a Windows system, for example from::

  S:\data\2019-02\user_name\test.h5 

to a Linux path::

  /local/data/user_name/

1. Install `FDT <http://monalisa.cern.ch/FDT/>`_ on both Windows and Linux.

2. On the Linux machine, start the FDT server::

     $ cd /local/data/fdt
     $ java -jar fdt.jar

3. On the Windows machine, from a Command Prompt, start the FDT client::

     C:\> cd C:\Users\se2admin\Desktop\FDT\
     C:\Users\se2admin\Desktop\FDT> java -jar fdt.jar -c handyn -d /local/data/Dunand/ S:\data\2019-02\Dunand\test.h5