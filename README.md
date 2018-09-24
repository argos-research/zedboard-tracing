# DS-5
<!-- FMC = FPGA Mezzanine Card (FMC) -->

According to [Arm Developer][1] the TPIO lines from the Xilinx SoC must be routed out to the appropriate expansion header (for off-chip parallel trace).
Proposed solution for the ZC702 can be found [here][2] and the Master answer record [here][3].
From there one can find an example design for the ZED board, available [here][4].
According to the [Arm DSTREAM User Guide][5] from the [DS-5 docs][6] using the Mictor cable should be enough for both debugging and tracing.

# [Vivado Constraints][12]
- Written in tcl
- LOC => Location Constraints
  > Places a logical element from the netlist to a site on the device

- PACKAGE_PIN
  > Specifies the location of a design port on a pin of the target device package.

- IOSTANDARD
  > IOSTANDARD sets an IO Standard to an I/O buffer instance.

# Troubleshoot
> Top module not set for fileset 'sources_1'.

Right-click on the BD in the sources window and click "Generate HDL wrapper" - taken from [here][7].

# Fix ZED Design
Pin layout is different for ZC702 and ZED Board.
According to the ZED board [schematics][8] (page 9) D21 and E21 connect to FMC_LA27 which is mapped to the J20 Connector on the MX105, instead of J16.
See also the [schematics][9] of the ZC702 as reference (page 7).
Therefore B15 should be used for PJTAG_TMS and C15 should be used for PJTAG_TCK instead.
Additionally it makes sense to use B16 for PJTAG_TDI and B17 for PJTAG_TDO.
As additional reference use the FMC XM105 [doc][10] and look at the J16 and J20 Connector.
To understand the connection of the TRACE_DATA pins on (e.g. TRACE_DATA[0] -> PIN38 -> FMC_LA10_P), take a look at the [Mictor 38 pinouts documentation][11].

# Wiring
The PJTAG is routed to the J16 connector on the XM105 and must be connected to the J19 connector (which loops through to the Mictor connector):
- TCK: J16-6 -> J19-4
- TMS: J16-8 -> J19-9
- TDI: J16-10 -> J19-7
- TDO: J16-12 -> J19-6

# Trace with DS-5
Use pause instead of stop tracing, otherwise unknown address/instructions errors are observable.

# Exkurs MIO und EMIO
## MIO
The I/O Peripherals (IOP) unit contains the data communication peripherals and communicates to external devices through a shared pool of up to 54 dedicated multiuse I/O (MIO) pins.
These MIO pins are software-configurable to connect to any of the internal I/O peripherals and static memory controllers.
If additional I/O pins beyond the 54 are required, it is possible to route these through the PL to the I/O associated with the PL.
This feature is referred to as extendable multiplexed I/O (EMIO).

## EMIO
Extendable multiplexed I/O (EMIO) allows unmapped PS peripherals to access PL I/O.

[1]: https://developer.arm.com/docs/137812646/latest/using-ds-5-with-xilinx-zynq-7000-devices
[2]: https://www.xilinx.com/support/answers/46915.html
[3]: https://www.xilinx.com/support/answers/50863.html
[4]: https://www.xilinx.com/support/answers/52095.html
[5]: https://static.docs.arm.com/100955/0529/arm_ds5_arm_dstream_user_guide_100955_0529_00_en.pdf
[6]: https://developer.arm.com/products/software-development-tools/ds-5-development-studio/docs
[7]: https://forums.xilinx.com/t5/Welcome-Join/Error-Common-17-70-Application-Exception/td-p/776328
[8]: https://www.xilinx.com/support/documentation/university/XUP%20Boards/XUPZedBoard/documentation/ZedBoard_RevC.1_Schematic_130129.pdf
[9]: https://www.xilinx.com/support/documentation/boards_and_kits/zc702_Schematic_xtp185_rev1_0.pdf
[10]: https://www.xilinx.com/support/documentation/boards_and_kits/ug537.pdf
[11]: https://developer.arm.com/docs/dui0499/latest/arm-dstream-target-interface-connections/the-mictor-38-connector-pinouts-and-interface-signals/mictor-38-pinouts
[12]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2012_2/ug903-vivado-using-constraints.pdf
