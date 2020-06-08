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

## Case

The Fractal Design Core 500 case is a Mini-ITX-compatible case with room for a real power supply and a fair number of drives.  I spent a lot of time shopping cases and wasn't sure I could get anything smaller that would hold at least six 2.5" drives.  This one had a 5.25" front space, which would fit the itty-bitty SAS/SATA backplane I found.

## Power Supply

I really like EVGA's power supplies, but I wish I'd sprung for a modular one.  I have a lot of extra power leads that have to be managed.  This one puts out 500W which is more than I will ever need, but it also supports lots of SATA drive connections without having to get molex-to-SATA-power adapters.

## Motherboard

It's important to realize that a NAS does not have heavy CPU needs.  This $75 Celeron CPU/motherboard combo has two memory slots, one PCIe slot, and two SATA connections onboard.  The one catch is that this motherboard is UEFI-only.  That means that there is no BIOS, which means it can't boot operating systems that require a BIOS, like DOS (including FreeDOS), which is still what a lot of manufacturers assume you'll do to flash firmware.  Fortunately, everything could be done via the UEFI shell given the right tools in this repository.

## SATA Backplane

I saw this on NewEgg and fell in love.  It's a SATA backplane that holds eight 2.5" drives (7mm max height), with hot-swap bays, and fits in a 5.25" CD-ROM bay.  There is no need for hot-swappable drives in a home NAS, but I couldn't resist.  If you want to do this build much cheaper, get a bigger case and use the largest 3.5" drives you can manage.

The backplane itself has two SATA power connections and eight SATA ports and it's own fan.  Sooo cute.

## SSD Drives

I wanted the OS on SSD, these 120GB SSDs are perfect.  I was originally planning to partitiion them and also use them as ZFS cache and log space, but since my workloads are pretty write-intensive, this wasn't really necessary and would have just made trouble if one went.  I have the OS mirrored across both SSDs so if one goes you're still ok.

## Data drives

These drives were the cheapest 2.5", 7mm height drives with at least a terabyte of storage.  I'm not sure if the fancy spec is really that fancy, as I still haven't benchmarked them.  Remember, the I in RAID is for Inexpensive, and with the small form factor I've already bought into a premium.

Two boot SSDs and six data drives mean I can run RAIDZ2 in NFS and suffer two drive failures without data loss.

## Memory

A ZFS rule of thumb is to have a gigabyte of ram per terabyte of storage, so having 16GB of RAM gives me room to grow.  I initially bought a single 16GB stick, but it was DOA.  Given the opportunity for further reflection on the motherboard manual, I saw that this board will access two memory sticks in parallel if they're matching sizes.  So two 8GB sticks instead.

## SAS Controller

Here is where life gets interesting.  FreeNAS, being a FreeBSD derivative, is not interested in your cheap-ass $20 SATA controller.  Noo, but it loves these LSI8000-based controllers, such as the LSI 9211-8i that you can get on Ebay for (drum roll) $25.  They come in a lot of shapes and sizes, the most commonly used are branded IBM or Dell.  The key is that it should be based on the LSI8000 controller CPU and should have two SAS ports.  With two SAS ports, I can use SAS-to-SATA expander cables to support up to eight SATA drives.

These controllers as shipped are set up to do hardware RAID, but I want to do everything in software through the magic of ZFS.  One advantage of that is if the motherboard or controller dies, I can stick these drives in a completely different machine and the ZFS pool will be perfectly usable.  The thing is, they need firmware flashing in order to put them in "IT" mode.

You need the following tools from the Broadcom (nee Avago nee Broadcom nee LSI) support website at https://www.broadcom.com/products/storage/host-bus-adapters/sas-9210-8i
