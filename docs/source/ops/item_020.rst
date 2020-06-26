TomoScan
========

.. contents:: 
   :local:

start the tomo medm screen::

   [user2bmb@arcturus]$ start_tomo


.. image:: ../img/fastTomo.png 
   :width: 480px
   :align: center
   :alt: fastTomo

medm screen
-----------

You can access `tomoScan <https://tomoscan.readthedocs.io/en/latest/index.html>`_ medm screen by clicking on the blue icon on the top right corner.

.. image:: ../img/tomoScan.png
   :width: 480px
   :align: center
   :alt: tomoScan

The tomoScan allows to configure and collect a single tomographic dataset. 

Python/Epics servers
--------------------

If in the tomoScan screen the Python server is not running (status is stopped) you can start it with::

    [user2bmb@pg10ge]$ cd ~/epics/synApps/support/tomoscan/iocBoot/iocTomoScan_2BM
    [user2bmb@pg10ge]$ python -i start_tomoscan.py

if in the tomoScan screen the PVs are not connected (white) you need to start the tomoScan IOC with::

    [user2bmb@arcturus]$ cd /home/beams/USER2BMB/epics/synApps/support/tomoscan/iocBoot/iocTomoScan_2BM
    [user2bmb@arcturus]$ ./start_IOC

cli
---

::

    [user2bmb@pg10ge]$ tomoscan -h
    usage: tomoscan [-h] [--config FILE] [--version]  ...
    optional arguments:
      -h, --help     show this help message and exit
      --config FILE  File name of configuration file
      --version      show program's version number and exit

      Commands:
  
    init         Create configuration file
    status       Show tomoscan status
    single       Run a single tomographic scan
    vertical     Run a vertical tomographic scan
    horizontal   Run a horizontal tomographic scan
    mosaic       Run a mosaic tomographic scan

each command help is accessible with ``-h``::

  Usage: tomoscan vertical [-h] [--scan-type SCAN_TYPE]
                         [--tomoscan-db-home FILE]
                         [--tomoscan-prefix TOMOSCAN_PREFIX]
                         [--in-situ-pv IN_SITU_PV]
                         [--in-situ-pv-rbv IN_SITU_PV_RBV]
                         [--in-situ-start IN_SITU_START]
                         [--in-situ-step-size IN_SITU_STEP_SIZE]
                         [--sleep-steps SLEEP_STEPS] [--sleep-time SLEEP_TIME]
                         [--vertical-start VERTICAL_START]
                         [--vertical-step-size VERTICAL_STEP_SIZE]
                         [--vertical-steps VERTICAL_STEPS] [--config FILE]
                         [--in-situ] [--logs-home FILE] [--sleep] [--testing]
                         [--verbose]

  optional arguments:
  -h, --help            show this help message and exit
  --scan-type SCAN_TYPE
                        For internal use to log the tomoscan status (default: )
  --tomoscan-db-home FILE
                        Log file directory 
                        (default: /home/user2bmb/epics/synApps/support/tomoscan/db/)
  --tomoscan-prefix TOMOSCAN_PREFIX
                        The tomoscan prefix, i.e.'13BMDPG1:TS:' or
                        '2bma:TomoScan:' (default: 2bma:TomoScan:)
  --in-situ-pv IN_SITU_PV
                        Name of the in-situ EPICS process variable to set
                        (default: )
  --in-situ-pv-rbv IN_SITU_PV_RBV
                        Name of the in-situ EPICS process variable to read back (default: )
  --in-situ-start IN_SITU_START
                        In-situ start (default: 0)
  --in-situ-step-size IN_SITU_STEP_SIZE
                        In-situ step size (default: 1)
  --sleep-steps SLEEP_STEPS
                        Number of sleep/in-situ steps (default: 1)
  --sleep-time SLEEP_TIME
                        Wait time (s) between each data collection scan (default: 0)
  --vertical-start VERTICAL_START
                        Vertical start position (mm) (default: 0)
  --vertical-step-size VERTICAL_STEP_SIZE
                        Vertical step size (mm) (default: 1)
  --vertical-steps VERTICAL_STEPS
                        Number of vertical steps (default: 1)
  --config FILE         File name of configuration file 
                        (default: /home/user2bmb/tomoscan.conf)
  --in-situ             Enable in-situ PV scan during sleep time (default: False)
  --logs-home FILE      Log file directory (default: /home/user2bmb/logs)
  --sleep               Enable sleep time between tomography scans (default: False)
  --testing             Enable test mode, tomography scan will not run (default: False)
  --verbose             Verbose output (default: False)

to run a single scan with the parameters set in the tomoScan IOC and the tomoscan-cli::

	[user2bmb@pg10ge]$ tomoscan single

tomoscan supports also vertical, horizontal and mosaic tomographic scans with::

    [user2bmb@pg10ge]$ tomoscan vertical
    [user2bmb@pg10ge]$ tomoscan horizontal
    [user2bmb@pg10ge]$ tomoscan mosaic

to run a vertical scan::

    $ [user2bmb@pg10ge]$ tomoscan vertical --vertical-start 0 --vertical-step-size 0.1 --vertical-steps 2

    2020-05-29 16:54:03,354 - vertical scan start
    2020-05-29 16:54:03,356 - vertical positions (mm): [0.  0.1]
    2020-05-29 16:54:03,358 - SampleInY stage start position: 0.000 mm
    2020-05-29 16:54:03,362 - single scan start
    2020-05-29 16:54:51,653 - single scan time: 0.805 minutes
    2020-05-29 16:54:51,654 - SampleInY stage start position: 0.100 mm
    2020-05-29 16:54:51,658 - single scan start
    2020-05-29 16:55:47,607 - single scan time: 0.932 minutes
    2020-05-29 16:55:47,607 - vertical scan time: 1.738 minutes
    2020-05-29 16:55:47,608 - vertical scan end

tomoscan-cli always stores the last used set of paramters so to repeat the above vertical scan::

    [user2bmb@pg10ge]$ tomoscan vertical

use ``-h`` for the list of supported parameters.

To repeat the vertical scan 5 times with 60 s wait time between each::

    [user2bmb@pg10ge]$ tomoscan vertical --sleep --sleep-steps 10 --sleep-time 60

to repeat the same::

    [user2bmb@pg10ge]$ tomoscan vertical --sleep

while::

    [user2bmb@pg10ge]$ tomoscan vertical

repeats a single vertical scan with --vertical-start 0 --vertical-step-size 0.1 --vertical-steps 5.

To reset the tomoscan-cli status::

	[user2bmb@pg10ge]$ tomoscan init

after deleting the tomoscan.conf file if already exists.
