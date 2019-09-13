Energy 
======

`energy <https://github.com/decarlof/tomo2bm/blob/master/flir/energy>`_ is a python script that changes the 2-BM beamline energy configuration. For help::

Examples
--------

::

    energy mono 24.9
    energy pink 2.657
    energy white

Help
----

::

    energy -h
    usage: energy [-h] {mono,pink,white} ...

    positional arguments:
      {mono,pink,white}  help for subcommand
        mono             set monochromatic energy
        pink             change to pink
        white            set to white beam

    optional arguments:
      -h, --help         show this help message and exit


    energy mono -h
    usage: energy mono [-h] energy

    positional arguments:
      energy      energy in keV; ex: energy mono 24.9

    optional arguments:
      -h, --help  show this help message and exit

    energy pink -h
    usage: energy pink [-h] angle

    positional arguments:
      angle       mirror angle in mrad; ex: energy pink 2.657

    optional arguments:
      -h, --help  show this help message and exit

    energy white -h
    usage: energy white [-h]

    optional arguments:
      -h, --help  show this help message and exit

