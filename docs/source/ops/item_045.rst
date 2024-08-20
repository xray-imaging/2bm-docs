FDT data transfer
=================


`Fast Data Transfer <https://fast-data-transfer.github.io>`_ is an open source application for efficient data transfers capable of 
reading and writing at disk speed over wide area networks (with standard TCP)

Linux to Linux
--------------

At the APS FDT in available at /APSshare/bin/fdt.jar, in the example below replace /APSshare/bin/ with the folder 
where you installed fdt.jar.


File copy
~~~~~~~~~

To copy files from the data collection machine (pg10ge) to the data analysis machine (tomo1):

#. start the fdt server on tomo1:

::

   [tomo@tomo1]$ java -jar /APSshare/bin/fdt.jar -S

#. start the data transfer from pg10ge to tomo1 using the following syntax:

::

   [user2bmb@pg10ge]$ java -jar /APSshare/bin/fdt.jar -c {remote_server} -d {remote_dir} {local_fname}

For example:
::

   [user2bmb@pg10ge]$ java -jar /APSshare/bin/fdt.jar -c tomo1 -d /data/2022-11/Kemner/ /local/data/2022-11/Kemner/*.h5

Directory copy
~~~~~~~~~~~~~~

To copy a directory between two linux computers, i.e. from tomodata1 to tomodata2:

#. start the fdt server on tomodata2:

::

   [tomo@tomodata2]$ java -jar /APSshare/bin/fdt.jar -S

#. start the data transfer from tomodata1 to tomodata2 using the following syntax:

::

   [tomo@tomodata1]$ java -jar /APSshare/bin/fdt.jar -c {remote_server} -d {remote_dir} {local_fname}

For example:
::

   [tomo@tomodata1]$ java -jar /APSshare/bin/fdt.jar -c tomodata2 -r -d /data2/tomodata1_backup /data/Lu-2023-03/

Verify transfer
~~~~~~~~~~~~~~~

You can verify the folder size with:
::

    [tomo@tomodata2]$ du /data2/tomodata1_backup/Lu-2023-03/

and the number of files with:
::

    [tomo@tomodata2]$ find /data2/tomodata1_backup/Lu-2023-03/ -type f | wc -l


Windows to Linux
----------------

To copy data from windows from:: 

   S:\data\2019-02\user_name\test.h5 

to linux machine::

   /local/data/user_name/ 
   
first install `FDT <http://monalisa.cern.ch/FDT/>`_ on both machines then go to the linux 
machine start the fdt server::

    $ cd /local/data/fdt
    $ java -jar fdt.jar

then go to the windows machine from a dos prompt start the copy (client)::

    $ cd C:\Users\se2admin\Desktop\FDT\
    $ java -jar fdt.jar -c handyn -d /local/data/Dunand/ S:\data\2019-02\Dunand\test.h5
