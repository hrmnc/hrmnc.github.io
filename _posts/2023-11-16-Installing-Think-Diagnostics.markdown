---
layout: post
title:  "Installing Think Diagnostics v2.2.5"
#category: Think
---
[Download Think TechCenter v2.2.5 here.](https://mega.nz/file/cH1hHT7B#HrxTiXoa9IFf9mPLhMzfcEUHYqPbj9fMB9WzeEhPJuU)<br>
Thanks to Frank Smith for providing the software!

Think TechCentre version 2.2.0 and up uses a new J2534 PassThru loader that allows any J2534-1 compliant adapter to be used. The configuration file just needs to be modified to tell TechCentre what adapter to use.

The J2534 adapter to be used is specified by three keys in the config file: *PassThruName*, *PassThruVersion*, and *PassThruRegistry*.

![Config file](/assets/config225.jpg)

*PassThruVersion* value can be set to "DashOne" or "DashTwo" (it doesn't matter which because TechCentre loads drivers for "DashOne" regardless).

For *PassThruName* and *PassThruRegistry* values, you'll have to look up your J2534 adapter identifiers. ***PassThruName* and *PassThruRegistry* values are case sensitive and should be copied and pasted exactly as they appear in your registry.** Run regedit and search for "PassThruSupport.04.04" to find your installed J2534 adapters:

![Registry view](/assets/reg225.jpg)

The examples shown above configure TechCentre to use the [Peak PCAN-USB](https://www.peak-system.com/PCAN-USB.199.0.html) with [PassThruAPI](https://www.peak-system.com/PCAN-PassThru-API.405.0.html). This setup will read the CAN modules in the car, but not the K-line modules.

For a cheaper adapter that will read ALL the modules in the car, the [GODIAG GD101](https://www.amazon.com/GODIAG-ELMConfig-ScanMaster-Automatically-Diagnosis/dp/B0BLYSD9M6) works great.
