# home-nas-build
As a covid project, I built a home network-attached storage server for family photos.  This isn't backup, but rather storage that can survive disk failures and bitrot.

The design goals were to stay under $1000, support at least 4TB of reliable storage, and be small.  I planned to run FreeNAS, as I've had good experiences with it, and ZFS scrubbing would take care of watching for bitrot in largely write-only files.

## Shopping list
1. Fractal Design Core 500 case
1. EVGA 500 W1 100-W1-0500-KR 80+ WHITE 500W Power Supply
1. ASRock J4105B-ITX Intel Celeron Mini ITX Motherboard / CPU Combo
1. Athena Power BP-15827SAC 5.25" Drive Bay to 8 x 2.5" SSD / HDD Hot-Swap SAS / SATA Backplane Module 
1. 2x Crucial BX500 2.5" 120GB SATA III 3D NAND Internal Solid State Drive
1. 6x Seagate FireCuda Gaming SSHD 1TB SATA 2.5" drive (ST1000LX015)
1. 2x 8GB PC-19200 DDR4 SODIMM DRAM
1. Dell PERC H200 SAS controller
