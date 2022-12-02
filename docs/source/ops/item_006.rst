FDT data transfer
=================

.. contents:: 
   :local:



Linux to Linux
--------------

To copy data from the data collection machine (pg10ge) to the data analysis machine (tomo1):

#. start the fdt server on tomo1:

::

   [tomo@tomo1]$ java -jar /APSshare/bin/fdt.jar -S

#. start the data transfer from pg10ge to tomo1 using the following syntax:

::

   [user2bmb@pg10ge]$ java -jar /APSshare/bin/fdt.jar -c {remote_server} -d {remote_dir} {local_fname}

For example:
::

   [user2bmb@pg10ge]$ java -jar /APSshare/bin/fdt.jar -c tomo1 -d /data/2022-11/Kemner/ /local/data/2022-11/Kemner/*.h5


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
