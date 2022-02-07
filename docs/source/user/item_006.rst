Data analysis
=============

At the APS
----------

Your raw data are automatically copied from the detector to the analysis computer (handyn in this example) under the folder /local/data/YYYY-MM/PI_lastName. 

Manual
~~~~~~

To manually reconstruct a data set, use the `tomopy cli tool <https://github.com/tomography/tomopy-cli>`_. 
::

    [tomo@handyn,~]$ bash
    [tomo@handyn,~]$ conda activate tomopy

then for help::

    [tomo@handyn,~]$ tomopy recon -h

To do a test reconstruction type::

    [tomo@handyn,~]$ tomopy recon --file-name /local/data/YYYY-MM/PI_lastName/file.h5 


Automatic
~~~~~~~~~

To setup a reconstruction to start automatically type::

    [tomo@handyn,~]$ bash
    [tomo@handyn,~]$ auto_rec /local/data/YYYY-MM/PI_lastName/

auto_rec runs tomopy recon for each newly transferred data set with the following options::

    tomopy recon --reconstruction-type try --file-name /local/data/YYYY-MM/PI_lastName/data.h5


At your home institution
------------------------

Install the following:

1. Download and install `anaconda python <https://www.anaconda.com/download/>`_ for your operative system.
2. Create a conda environment:
    
::

    $ conda create -n tomopy python=3.9

3. Activate the newly created conda environment:

::

    $ conda activate tomopy


4. Install `tomopy <https://tomopy.readthedocs.io/en/latest/>`_:

::

    $ conda install --channel conda-forge tomopy


5. Install `dxchange <https://dxchange.readthedocs.io/en/latest/>`_:

::

    $ conda install -c conda-forge dxchange

6. Install `tomopy cli <https://tomopycli.readthedocs.io/en/latest/>`_:

::

    $ git clone https://github.com/tomography/tomopy-cli.git
    $ cd tomopy-cli
    $ python setup.py install

7. Install `tomopy cli dependecy <https://github.com/tomography/tomopy-cli/blob/master/requirements.txt>`_:

::

    pip install opencv-python


To run a reconstuction you can now run::

    $ tomopy recon --file-name /data/file.h5


Mosaic
------

For samples larger than the filed of view we collect multiple data sets consisiting of overlapping tiles to form a mosaic.
To reconstruct these type of data please use `mosaic <https://github.com/xray-imaging/mosaic>`_  command-line-interface for mosaic tomography data processing.
An example of a mosaic dataset can be found on `tomobank <https://tomobank.readthedocs.io/en/latest/source/data/docs.data.tomosaic.html#foam>`_ 
