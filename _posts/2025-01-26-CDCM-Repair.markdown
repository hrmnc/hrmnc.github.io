---
layout: post
title:  "CDCM (Climate and Defrost Control Module) Repair"
category: Think
tag: Think
---
The air conditioning system in my Think was not working when I bought it. Upon sniffing the CAN bus, I discovered that the CDCM module was not transmitting the expected 0x40* packets. I was able to get a replacement CDCM from ThinkParts4U, so I tore into my defective unit to find out what went wrong.

![PCB](/assets/20250126-PCB.jpg)

At the heart of the CDCM is a Microchip PIC18F4580. Curiously, Think chose to use an industrial-grade part rather than automotive-grade. I guess the lower operating temperature range is not a concern for Scandinavian climates. 

The PIC talks to other controllers in the car through 2 CAN busses, identified in the wiring diagram as Vehicle CAN and Inverter CAN. The Inverter CAN bus is used solely to control the inverter in the air conditioning electric compressor. 

![CAN Overview](/assets/20250126-CAN.png)

The PIC talks on the Vehicle CAN bus through a MCP2515 controller and a PCA82C251 transceiver, while the Inverter CAN uses the PIC's internal CAN controller and another PCA82C251 transceiver. There is a 25LC640 EEPROM next to the PIC, but mine dumped out to be blank so I'm uncertain of it's purpose. Possibly fault code storage?

![ICSP port](/assets/20250126-ICSP.jpg)
The PIC can be programmed through the ICSP port labelled "PROG". The pinout is as follows:
1. GND
2. MCLR
3. PGD
4. PGC
5. VCC

<br>
## Recovering Bricked CDCM Modules
The official way to upgrade firmware on CDCM/VCU modules is using Think's *"CCP Tool"*. This Windows-based program upgrades the CDCM through the Vehicle CAN bus using reserved CAN IDs. The program starts by telling the PIC to program a flag in the highest byte of the PIC's internal EEPROM that signals the bootloader to boot into a firmware upgrade mode. This flag is cleared at the end of a successful firmware upgrade, but if the firmware upgrade process fails or is interrupted then the flag is never cleared and the PIC will be stuck in firmware upgrade mode. And since the CDCM is already in FW upgrade mode, *CCP Tool* will hang because it never gets a response from the CDCM. So it becomes necessary to open the CDCM and reprogram the PIC through the ICSP ports.

Here are the program and EEPROM hex files for the CDCM rev0.9.2, the final North American release with PTC heater support:
* [**Program Flash**](/assets/CDCM/0.9.2Flash.BIN)
* [**EEPROM**](/assets/CDCM/0.9.2EEPROM.BIN)

Both program flash and EEPROM need to be reprogrammed on bricked units to ensure that the FW upgrade flag is cleared. You can use a PICKIT3, but I had a XGecu T48 that was able to do the job.
![PIC Configuration](/assets/20250126-PICconfiguration.jpg)
PIC Configuration bits

## CAN Transceiver Repair

It turns out that the PCA82C251 transceiver on the Vehicle CAN side of my CDCM had burnt out. That was why I was not seeing any packets from the CDCM on the bus. [***AH1014***](https://www.nxp.com/docs/en/supporting-information/AH1014.pdf) says that the TJA1042 third-gen transceiver is backwards compatible with the PCA82C251 first-gen transceiver. I intend to buy some the next time I order from Digikey. In the meantime, I simply desoldered the working transceiver from the Inverter CAN and put it on Vehicle CAN to verify that the part was indeed defective.
![AH1014](/assets/20250126-AH1014.png)

But why did the transceiver burn out? When a garage did the PTC heater upgrade on my car, they had stripped one of the grounding point bolt that grounds the CDCM and HVAC system. This caused a floating ground on the blower fan, which caused a huge induced voltage spike whenever it was shut off. I suspect either this voltage spike or the floating ground caused the CDCM's CAN transceiver to burn out. It also eventually shorted out my blower fan windings, causing the blower resistors to burn out and the 30A blower fuse to pop. So the lesson learned is to **always make sure your grounds are solid.**

In my next post I will explore the Vehicle Control Unit (VCU), which also suffered a CAN transceiver failure resulting from a failed firmware upgrade (entirely my fault this time.)
