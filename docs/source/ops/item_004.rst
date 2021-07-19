Data Management
===============

Data ownership and local storage location is managed by `Dmagic <https://dmagic.readthedocs.io/en/latest/index.html>`_ by automatically retrieving user information from the APS scheduling system and updating the user info PVs at:


.. image:: ../img/medm_screen.png 
   :width: 480px
   :align: center
   :alt: tomo_user


To run a user PV adptate::

    [user2bmb@arcturus]$ bash
    [user2bmb@arcturus]$ source /home/dm_bm/etc/dm.setup.sh
    [user2bmb@arcturus]$ dmagic show
    [user2bmb@arcturus]$ dmagic tag

you can also enter manually the user last name/email address then you can create standard (YYYY_MM/pi_last_name) user folders on petrel with:

Data management and sharing with users is manged by `Globus <https://dmagic.readthedocs.io/en/latest/index.html>`_. The Globus servers available at Argonnne are petrel#tomography and aps#data both accessible from the `Globus portal <https://www.globus.org/>`_.

You can select with server to use with::

    [user2bmb@arcturus]$ bash
    [user2bmb@arcturus]$ globus-server-name voyager (or petrel)

To automatically create YYYY-MM/PI_lastName/ on the globus server::

    [user2bmb@arcturus]$ bash
    [user2bmb@arcturus]$ globus dirs

and you can share this folder will all users listed in the scheduled proposal with::

    [user2bmb@arcturus]$ globus email --schedule

To upload data from the beamline to the Globus server

If using petrel#tomography, make a Globus copy between::

    [user2bmb@pg10ge]$  /local/data/YYYY-MM/PI_lastName/

and the same YYYY-MM/PI_lastName/ on  `petrel <https://app.globus.org/file-manager?origin_id=e133a81a-6d04-11e5-ba46-22000b92c6ec&origin_path=%2F2-BM%2F>`_.

If using the aps#tomography::

    [user2bmb@arcturus]$ source /home/dm_bm/etc/dm.setup.sh
    [user2bmb@arcturus]$ dm-station-gui

For more details see the `DM instruction <https://confluence.aps.anl.gov/display/DMGT/2-BM+Deployment>`_.

