Module
======

Adding
======

To add a software package, i.e. HDFView, as a new module::

    $ cd ~/Software
    $ tar -xvzf HDFViewApp-3.3.1-centos7_64.tar.gz
    $ mv HDFView HDFView-3.3.1
    $ mkdir -p /home/beams/TOMO/Modules/modulefiles/HDFView/
    $ cd /home/beams/TOMO/Modules/modulefiles/HDFView/
    $ touch 3.3.1

Edit 3.3.1 to add::

    #%Module
    proc ModulesHelp { } {
       puts stderr "This module adds HDFView to your path"
    }
    module-whatis "This module adds HDFView to your path\n"
    set basedir "/home/beams/TOMO/Software/HDFView"
    prepend-path PATH "${basedir}/bin"
    prepend-path LD_LIBRARY_PATH "${basedir}/lib"

then edit ~/.bashrc to add::

    module use --append /home/beams/TOMO/Modules/modulefiles/

Check your new module is available with::

    $ module avail
    ------------------- /home/beams/USER2BMB/Modules/modulefiles -------------------
    HDFView/3.3.1

Loading
=======

To load a module::

    module load HDFView/3.3.1

Using
=====

To use a module::

    $ HDFView


To add permanently module to the shell add in ~/.chrc::

    module use --append /home/beams/TOMO/Modules/modulefiles/
    module load HDFView/3.3.1


