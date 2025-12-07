=========
Computing
=========

The following computers are in use in the Imaging Group.

IT standard IOC platforms
=========================

Recommended rack-mount IOC, SSF/Mini/IOC, and workstation/NX platforms can
be ordered following the instructions provided `here
<https://anl.box.com/s/iw6hpbnl09htihvp25eiek2kxs54iwsd>`_.

Data analysis and storage
=========================

.. _cluster_folder: https://anl.box.com/s/cwqbvet2qv8239nhrof0qemyohd0jho3
.. _cluster: https://anl.box.com/s/uysvb5ujnlugmd16r2f6o10fem9rjgvr
.. _cluster_ram: https://anl.box.com/s/0iueo9mnndywf85ajyagtr7kfu6r1zrh
.. _cluster_01: https://anl.box.com/s/oc9g49r6an1lcwh0d5gzisno6ef5yni1
.. _cluster_02: https://anl.box.com/s/7onv5ju2rt42w15uz689pbuslfelpvz9
.. _cluster_quote: https://anl.box.com/s/j7wz6li4afoq2gs5g8feehmmz8q7whuy
.. _cluster_quote_01: https://anl.box.com/s/06nkozbmkhu5qsi61njcgm1qs3ug8pcg
.. _cluster_quote_02: https://anl.box.com/s/hz9l2whlju2a81tyr4k9e07ukc8m4zkn
.. _disk_array_01: https://anl.box.com/s/zzyvv7w80ltwbtf09zrjiqiw7ak6i7ge
.. _disk_array_quote_01: https://anl.box.com/s/sbft8cbt2xcpzuuvikixr82dn9jf6zog
.. _disk_array_02: https://anl.box.com/s/d8b1xb6e99e6vggqv5dd9z02luefo7hw
.. _disk_array_quote_02: https://anl.box.com/s/o1sh7nfxzqhcb6qef19f9s7ogavobv0g
.. _disk_array_03: https://anl.box.com/s/2qssygdx83qkwo8up448khrzd26fm08p
.. _disk_array_quote_03: https://anl.box.com/s/bd2i81zg4kcgecp4kd8740udf2fqwii7
.. _ssd_array_quote_03: https://anl.box.com/s/tmwq8cpiicl378c62yxbu6r3gnlek7lu
.. _eberlight_01: https://anl.box.com/s/njzf1ya4vlryd6bc3a61fn54g4nkol7o
.. _eberlight_02: https://anl.box.com/s/cd77y9uwtesx2cfo60q69ekt1ua9wrc4

+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| Station   | Name               | Product       | Part list         | Model                                     | Quote                  |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomo 1-2           | MNJ15421064   | `cluster`_        | Supermicro 740GP-TNRT node                | `cluster_quote`_       |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomo 1-2 (RAM)     | MNJ15590206   | `cluster_ram`_    | 16× 128GB MEM-DR412L-SL01-ER32            | `cluster_ram`_         |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomo 3             | MNJ15421064   | `cluster_01`_     | Supermicro 740GP-TNRT node                | `cluster_quote_01`_    |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomo 4-5 (SXM-4)   | MNJ16235754   | `cluster_02`_     | Supermicro 220GQ-TNAR+ node               | `cluster_quote_02`_    |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomodata1          | MNJ15508749   | `disk_array_01`_  | SYS-220U-TNR storage                      | `disk_array_quote_01`_ |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomodata2          | MNJ18897861   | `disk_array_02`_  | SYS-220U-TNR storage                      | `disk_array_quote_02`_ |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomodata3          | MNJ18897861   | `disk_array_03`_  | SYS-220U-TNR storage                      | `disk_array_quote_03`_ |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomodata3 SSD      | MNJ21584605   | 12× SSD 15 TB     | Solidigm P5316 15.36T NVMe PCIe4x4 QLC    | `ssd_array_quote_03`_  |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| 2-BM      | tomdet             |               |                   | Supermicro SYS-521E-WR                    |                        |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+
| eBERlight | eberproc1-2        | MNJ16187026   | `eberlight_01`_   | Supermicro 740GP-TNRT node                | `eberlight_02`_        |
+-----------+--------------------+---------------+-------------------+-------------------------------------------+------------------------+

Current memory configuration for tomo1–5 and the disk-array configuration
for tomodata1–3 are documented in `this spreadsheet
<https://anl.box.com/s/ywjtwzk6q9su93pizzbdk90q77g0doo7>`_.

Data collection
===============

.. _pg10ge label: https://anl.box.com/s/oslaky958be3vyifda2xyq4tv0v9v7pz
.. _pg10ge SM: https://anl.box.com/s/m1u8o62wbr27n26iotfnbhgpncwsapcq
.. _lyra label: https://anl.box.com/s/lrjiwsfzwbe51gueb6vpyinqav86qx6o
.. _lyra SM: https://anl.box.com/s/dv0ub0gdjhs7q3h50ehgro6gaesbxcjf
.. _tomdet label: https://anl.box.com/s/b6qqmbplxsbxjbpmfkdb8ayrzabo9w4x
.. _tomdet SM: https://anl.box.com/s/67l25mjm9vkoxnbkydjubfl3ge9wmvs2
.. _SYS 521E WR: https://www.supermicro.com/en/products/system/up/2u/sys-521e-wr
.. _tomdet part list: https://anl.box.com/s/ypx1kn3ejyqpl934otd9kxug8gdjapxn

Detector-control computers at 2-BM:

+-----------+--------------+-------------------+-----------------+--------------------------+----------------------------------------+
| Station   | Name         | Model             | Product No.     | Serial No.               | Manual                                 |
+-----------+--------------+-------------------+-----------------+--------------------------+----------------------------------------+
| 2-BM-A    | pg10ge       | HP Z8 G4          | 3GF37UT#ABA     | `pg10ge label`_          | `pg10ge SM`_                           |
+-----------+--------------+-------------------+-----------------+--------------------------+----------------------------------------+
| 2-BM-B    | lyra         | HP EliteDesk 800  | P4K18UT#ABA     | `lyra label`_            | `lyra SM`_                             |
+-----------+--------------+-------------------+-----------------+--------------------------+----------------------------------------+
| 2-BM-B    | tomdet (*)   | Supermicro        | `SYS 521E WR`_  | `tomdet label`_          | `tomdet part list`_ `tomdet SM`_       |
+-----------+--------------+-------------------+-----------------+--------------------------+----------------------------------------+

Compatible memory modules for Supermicro SYS-521E-WR (*):

+-------------+------------+-----------------------+------------------------------------------------------+
| Type        | Capacity   | Part Number           | Description                                          |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-4800   | 16 GB      | MEM-DR516MB-ER48      | 16GB DDR5-4800 1RX8 (16Gb) RDIMM                     |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-4800   | 32 GB      | MEM-DR532MD-ER48      | 32GB DDR5-4800 2RX8 (16Gb) RDIMM                     |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-4800   | 64 GB      | MEM-DR564MC-ER48      | 64GB DDR5-4800 2RX4 (16Gb) RDIMM                     |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-4800   | 96 GB      | MEM-DR596NC-ER48      | 96GB DDR5-4800 2Rx4 LP (24Gb) ECC RDIMM              |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-5600   | 16 GB      | MEM-DR516MB-ER56      | 16GB DDR5-5600 1RX8 ECC RDIMM                        |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-5600   | 32 GB      | MEM-DR532MD-ER56      | 32GB DDR5-5600 2Rx8 ECC RDIMM                        |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-5600   | 64 GB      | MEM-DR564MC-ER56      | 64GB DDR5-5600 2Rx4 ECC RDIMM                        |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-5600   | 96 GB      | MEM-DR596NC-ER56      | 96GB DDR5-5600 2RX4 RDIMM                            |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-5600   | 128 GB     | MEM-DR512PC-ER56      | 128GB DDR5-5600 2Rx4 LP (32Gb) ECC RDIMM, HF, RoHS   |
+-------------+------------+-----------------------+------------------------------------------------------+
| DDR5-5600   | 128 GB     | MEM-DR512L-CL02-ER56  | 128GB DDR5-5600 2Rx4 (32Gb) ECC RDIMM, HF, RoHS      |
+-------------+------------+-----------------------+------------------------------------------------------+