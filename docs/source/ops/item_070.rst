===============================
Vibration Frequency Measurement
===============================


Reference Measurement
=====================

This measurement was performed at the **Advanced Photon Source (APS)**, beamline
**2-BM (Micro-tomography)**, as part of the **APS-U Commissioning** activities.
The data analyzed here were collected **before** the APS air handler are turned off. 
As such, this dataset serves primarily to **validate the vibration-analysis method** 
and to establish a **reference measurement** of the vertical vibration level under 
the current (house-ventilated) operating conditions.

The goal of the test was to characterize the **vertical vibration** of the
x-ray image formed on a scintillator screen by analyzing a high-speed image
sequence acquired with the 2-BM micro-CT detector system.

Experiment Summary
------------------

A stack of projection images was acquired with a FLIR Oryx camera viewing a
LuAG scintillator at **20 keV** beam energy. The camera operated at
approximately **99 fps** (from file name ``S01-AHU401_1000frms_99fps_001.h5``),
and the detector configuration (objective lens, binning, and ROI) was chosen for
micro-tomography imaging with relatively large field of view. The resulting 3D
dataset (frames × height × width) was analyzed to determine the dominant
vertical vibration frequencies of the image.

The analysis used the script
`frequency.py <https://github.com/decarlof/sandbox/blob/master/vibration/frequency_folder.py>`_ 
and applied a **position-based cross-correlation method** to estimate frame-to-frame
vertical motion, followed by FFT-based frequency analysis. The frequency search
was partitioned into two bands:

* **25–35 Hz**: typically associated with the ventilation / mechanical lines.
* **35–100 Hz**: to capture possible structural resonance modes.

Instrument and Acquisition Metadata
-----------------------------------

General information
^^^^^^^^^^^^^^^^^^^

+-----------------------------------+---------------------------------------------------------------------+
| Item                              | Value                                                               |
+===================================+=====================================================================+
| Facility                          | Advanced Photon Source (APS)                                        |
+-----------------------------------+---------------------------------------------------------------------+
| Beamline                          | 2-BM                                                                |
+-----------------------------------+---------------------------------------------------------------------+
| Instrument                        | Micro-tomography                                                    |
+-----------------------------------+---------------------------------------------------------------------+
| Experiment title                  | APS-U Commissioning Vibration Test                                  |
+-----------------------------------+---------------------------------------------------------------------+
| Experimenter                      | DeCarlo                                                             |
+-----------------------------------+---------------------------------------------------------------------+
| Institution                       | Argonne National Laboratory                                         |
+-----------------------------------+---------------------------------------------------------------------+
| Proposal ID                       | 00000                                                               |
+-----------------------------------+---------------------------------------------------------------------+
| Raw data file                     | ``S01-AHU401_1000frms_99fps_001.h5``                                |
+-----------------------------------+---------------------------------------------------------------------+
| File path                         | ``/data2/2025-12/DeCarlo/reference/``                               |
+-----------------------------------+---------------------------------------------------------------------+
| Acquisition start                 | 2025-12-19T22:18:10-0600                                            |
+-----------------------------------+---------------------------------------------------------------------+
| Acquisition end                   | 2025-12-19T22:21:13-0600                                            |
+-----------------------------------+---------------------------------------------------------------------+
| Storage ring current              | 130.0429941781159 mA                                                |
+-----------------------------------+---------------------------------------------------------------------+
| Fill mode                         | 130 mA / 48 singlets / High Coupling                                |
+-----------------------------------+---------------------------------------------------------------------+


X-ray source and optics
^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------------------+------------------------------------------------------+
| Item                                      | Value                                                |
+===========================================+======================================================+
| Source name                               | Advanced Photon Source                               |
+-------------------------------------------+------------------------------------------------------+
| Beamline                                  | 2-BM                                                 |
+-------------------------------------------+------------------------------------------------------+
| Monochromator name                        | 2-BM-A double multilayer monochromator               |
+-------------------------------------------+------------------------------------------------------+
| Monochromator mode                        | 0 (mono)                                             |
+-------------------------------------------+------------------------------------------------------+
| X-ray energy                              | 20.0 keV                                             |
+-------------------------------------------+------------------------------------------------------+
| Monochromator upstream arm (us\_arm)      | 0.7257932810105996 °                                 |
+-------------------------------------------+------------------------------------------------------+
| Monochromator downstream arm (ds\_arm)    | 0.7380825000000035 °                                 |
+-------------------------------------------+------------------------------------------------------+
| Mirror name                               | 2-BM Mirror                                          |
+-------------------------------------------+------------------------------------------------------+
| Mirror angle                              | 2.614850181232382 mrad                               |
+-------------------------------------------+------------------------------------------------------+
| Mirror stripe                             | 0 (Pt)                                               |
+-------------------------------------------+------------------------------------------------------+
| Mirror position x                         | 2.3624250000000018 mm                                |
+-------------------------------------------+------------------------------------------------------+
| Mirror position y                         | 0.00014500000000072788 mm                            |
+-------------------------------------------+------------------------------------------------------+


Slits configuration
^^^^^^^^^^^^^^^^^^^

+-------------------------------------------+------------------------------------------------------+
| Item                                      | Value                                                |
+===========================================+======================================================+
| Slits name                                | 2-BM slits                                           |
+-------------------------------------------+------------------------------------------------------+
| Upstream H center / size                  | -0.16999999999999993 mm / 2.000000000000001 mm       |
+-------------------------------------------+------------------------------------------------------+
| Upstream V center / size                  | -0.36 mm / 1.4000000000000001 mm                     |
+-------------------------------------------+------------------------------------------------------+
| Downstream H center / size                | 0.20599999999999952 mm / 3.7 mm                      |
+-------------------------------------------+------------------------------------------------------+
| Downstream V center / size                | 19.895787499999987 mm / 3.542425000000026 mm         |
+-------------------------------------------+------------------------------------------------------+


Attenuator configuration
^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------------------+------------------------------------------------------+
| Item                                      | Value                                                |
+===========================================+======================================================+
| Attenuator name                           | 2-BM-A user filters                                  |
+-------------------------------------------+------------------------------------------------------+
| Upstream position                         | 4 mm                                                 |
+-------------------------------------------+------------------------------------------------------+
| Upstream filter list                      | 0: 1 mm C; 1: 150 µm Al; 2: 600 µm Al; 3: 1 mm Al;   |
|                                           | 4: None; 5: LowLimit                                 |
+-------------------------------------------+------------------------------------------------------+
| Additional filters                        | Manually added filters: None                         |
+-------------------------------------------+------------------------------------------------------+


Detector and imaging chain
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-------------------------------------------+------------------------------------------------------+
| Item                                      | Value                                                |
+===========================================+======================================================+
| Detection system type                     | Micro-CT with scintillator + objective + FLIR camera |
+-------------------------------------------+------------------------------------------------------+
| Scintillator type                         | LuAG                                                 |
+-------------------------------------------+------------------------------------------------------+
| Scintillator active thickness             | 50.0 µm                                              |
+-------------------------------------------+------------------------------------------------------+
| Objective magnification                   | 2.0×                                                 |
+-------------------------------------------+------------------------------------------------------+
| Objective tube length                     | 1.0 mm                                               |
+-------------------------------------------+------------------------------------------------------+
| Objective effective pixel size            | 1.725 µm                                             |
+-------------------------------------------+------------------------------------------------------+
| Camera manufacturer / model               | FLIR Oryx ORX-10G-51S5M                              |
+-------------------------------------------+------------------------------------------------------+
| Camera serial number                      | 19173710                                             |
+-------------------------------------------+------------------------------------------------------+
| Camera pixel size (sensor)                | 3.45 µm                                              |
+-------------------------------------------+------------------------------------------------------+
| Detector data type                        | UInt8 (Mono8)                                        |
+-------------------------------------------+------------------------------------------------------+
| Detector temperature                      | 41.375 °C                                            |
+-------------------------------------------+------------------------------------------------------+
| Max sensor size (X × Y)                   | 1224 × 1024 pixels                                   |
+-------------------------------------------+------------------------------------------------------+
| ROI size (X × Y)                          | 1024 × 1024 pixels                                   |
+-------------------------------------------+------------------------------------------------------+
| Binning (X, Y)                            | 2 × 2                                                |
+-------------------------------------------+------------------------------------------------------+
| Effective field of view (X, Y)            | 1024 px × 1.725 µm ≈ 1.77 mm                         |
|                                           | (per dimension, for 1×1; scaled accordingly for 2×2) |
+-------------------------------------------+------------------------------------------------------+
| Detector gain                             | 22.997265890825133                                   |
+-------------------------------------------+------------------------------------------------------+
| Exposure time                             | 0.009999 s                                           |
+-------------------------------------------+------------------------------------------------------+
| Acquire period                            | 0.006934384 s                                        |
+-------------------------------------------+------------------------------------------------------+
| Frame rate (from file name)               | 99 fps                                               |
+-------------------------------------------+------------------------------------------------------+
| Frame rate control                        | frame\_rate\_enable = No                             |
+-------------------------------------------+------------------------------------------------------+
| HDF5 plugin version                       | NDFileHDF5 ver1.10.1                                 |
+-------------------------------------------+------------------------------------------------------+
| ADCore version                            | 3.14.0                                               |
+-------------------------------------------+------------------------------------------------------+
| Detector driver version                   | 3.5.0                                                |
+-------------------------------------------+------------------------------------------------------+
| Detector SDK version                      | 4.0.0.116                                            |
+-------------------------------------------+------------------------------------------------------+


Vibration Analysis Method
-------------------------

The image sequence stored in ``/exchange/data`` of the HDF5 file was processed
using `frequency.py <https://github.com/decarlof/sandbox/blob/master/vibration/frequency_folder.py>`_.

Two complementary methods are implemented in the script; in this experiment
the reported values come from the **position-based method**:

1. **Position-based method**

   * For each frame, the vertical shift relative to the first frame is
     estimated using ``skimage.registration.phase_cross_correlation`` with
     an upsampling factor of 100.
   * The resulting series of absolute vertical positions (in pixels) is
     mean-subtracted and transformed with a real FFT (``np.fft.rfft``).
   * The magnitude spectrum is examined, and the dominant peak is located in
     a specified frequency band.

2. **Detrended shift-based method** (optionally available)

   * Frame-to-frame vertical shifts are computed (each frame relative to the
     next).
   * The shift series is linearly detrended and Hann-windowed.
   * FFT is applied and peaks are searched in the same band as above.

For this analysis, the script will typically be run with:

* Sampling rate derived from acquisition:  
  ``sampling_rate ≈ 1 / acquire_period ≈ 99 Hz``  
  (consistent with the acquisition period from the file name).

* Frequency bands:
  
  * ``[25.0, 35.0] Hz`` 
  * ``[35.0, 100.0] Hz`` 

* Frames used: a subset of the available frames (e.g., first 600–1000 frames)
  for robust FFT statistics.


Vibration Analysis Results
--------------------------

Processing configuration
^^^^^^^^^^^^^^^^^^^^^^^^

(Example configuration; update with actual values for this run.)


+-------------------------------------------+------------------------------------------------------+
| Item                                      | Value                                                |
+===========================================+======================================================+
| Script                                    | ``vibration/frequency.py``                           |
+-------------------------------------------+------------------------------------------------------+ 
| Input file                                | ``S01-AHU401_1000frms_99fps_001.h5``                 |
+-------------------------------------------+------------------------------------------------------+
| Dataset path                              | ``/exchange/data``                                   |
+-------------------------------------------+------------------------------------------------------+
| Sampling rate used                        | 99.0 Hz (``dt = 0.010101010101010102 s``)            |
+-------------------------------------------+------------------------------------------------------+
| Frames loaded                             | 1000                                                 |
+-------------------------------------------+------------------------------------------------------+
| Frames used for analysis (position-based) | 648                                                  |
+-------------------------------------------+------------------------------------------------------+
| Upsampling factor (phase correlation)     | 100                                                  |
+-------------------------------------------+------------------------------------------------------+
| Frequency resolution (FFT)                | 0.15278 Hz                                           |
+-------------------------------------------+------------------------------------------------------+
| Frequency bands analyzed                  | [25.0, 35.0] Hz and [35.0, 100.0] Hz                 |
+-------------------------------------------+------------------------------------------------------+


Dual-band vibration frequencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The dual-band analysis was performed with the **position-based method**:

+-------------------------------------------+-----------------+--------------------------------------------+
| Frequency band                            | Peak frequency  | Interpretation                             |
+===========================================+=================+============================================+
| 25.0–35.0 Hz                              | 29.944 Hz       | Dominant line in the "vent/line" band      |
+-------------------------------------------+-----------------+--------------------------------------------+
| 35.0–100.0 Hz                             | 46.139 Hz       | Dominant structural/resonance component    |
+-------------------------------------------+-----------------+--------------------------------------------+

(Values rounded to three decimal places as reported by the script.)


Summary and Remarks
-------------------

* Under the given beamline and detector conditions (20 keV, LuAG scintillator,
  2× objective, 2×2 binning, nominal 99 fps), the vertical image motion for
  run ``S01-AHU401_1000frms_99fps_001.h5`` is dominated by frequency components
  near **29.94 Hz** and **46.14 Hz**.

* The **29.94 Hz** component lies in the 25–35 Hz band and is consistent with a
  ventilation / mechanical line contribution.

* The **46.14 Hz** component lies in the 35–100 Hz band and indicates a higher
  frequency resonance, potentially associated with the detector mechanics,
  sample stage, or beamline infrastructure.

These values provide a quantitative baseline for comparison with future
measurements that will be taken after the air handler is turned off, enabling
assessment of the impact of HVAC-related vibrations on the imaging system.