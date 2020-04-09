Check list
==========

At the beginning of a new user beamtime login into user2bmb@arcturus then::

    [user2bmb@arcturus]$ start_tomo 

and check the user info screen:

.. image:: ../img/medm_screen.png 
   :width: 480px
   :align: center
   :alt: tomo_user

make sure the user PV are updated correctly from the scheduling system if not hit the update button for instructions or::

    [user2bmb@arcturus]$ dmagic tag


you can also enter manually the user last name/email address then:

as user2bmb@arcturus::

    [user2bmb@arcturus]$ bash
    [user2bmb@arcturus]$ globus dirs
    [user2bmb@arcturus]$ globus email --schedule

as user2bmb@pg10ge::

    [user2bmb@pg10ge]$ rm /home/user2bmb/tomo2bm.conf
    [user2bmb@pg10ge]$ tomo init

as tomo@mona3::

    [tomo@mona3]$ bash
    [tomo@mona3]$ auto_rec /local/data/YYYY-MM/PI_lastName/

At the end of the user beamtime make a Globus copy between

    [user2bmb@pg10ge]$  /local/data/YYYY-MM/PI_lastName/

    and the same YYYY-MM/PI_lastName/ on  `petrel <https://app.globus.org/file-manager?origin_id=e133a81a-6d04-11e5-ba46-22000b92c6ec&origin_path=%2F2-BM%2F>`_.