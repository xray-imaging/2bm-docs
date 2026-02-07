===========
EPICS Build
===========

There are situations where building an EPICS IOC from a ``csh`` or ``bash`` shell may fail, even though the same commands normally work.

For example, running:

::

  [2bmb@tomdet] $ make clean
  [2bmb@tomdet] $ make clean
  [2bmb@tomdet] $ make -sj

may fail with errors such as:

::

  make[1]: mkdir: Permission denied
  make[1]: *** /home/beams/2BMB/epics/epics-base/configure/RULES_ARCHS:70: O.Common Error 127
  make[1]: *** Waiting for unfinished jobs....
  make: *** /home/beams/2BMB/epics/epics-base/configure/RULES_DIRS:85: configure.Install Error 2

This typically indicates that ``make`` is attempting to create a directory in a location where it does not have write permissions (for example, ``O.Common`` under ``configure``).

Until the root cause is identified and fixed, the following workaround can be used.

Workaround
----------

Run ``make`` with a minimal ``PATH`` and disable parallel builds:

::

  [2bmb@tomdet] $ env PATH="/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin" make -j1

After this completes successfully, subsequent builds usually work as expected, including parallel builds:

::

  [2bmb@tomdet] $ make -sj

.. warning::

   If you encounter the error:

   ::

     configure/os/CONFIG.rhel9-x86_64.Common: No such file or directory

   make sure that ``EPICS_HOST_ARCH`` is set correctly.

   In a ``csh`` terminal:

   ::

     setenv EPICS_HOST_ARCH linux-x86_64

   In a ``bash`` terminal:

   ::

     export EPICS_HOST_ARCH=linux-x86_64
