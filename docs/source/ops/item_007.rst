Continuos rotation
==================

You can set multiple tomographic data sets to be collected continuously by setting::

    [user2bmb@arcturus]$ tomo scan --sample-rotation-start 0 --sample-rotation-end SRE --num-projections NP

with SRE up to 64,000 degree and NP < 400,000

for example to collect 100 (0-180) data sets with 1,500 projections each

SRE = 18,000 (180 * 100) and NP = 150,000::

    [user2bmb@arcturus]$ tomo scan --sample-rotation-start 0 --sample-rotation-end 18000 --num-projections 150000

